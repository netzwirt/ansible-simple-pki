# ansible-simple-pki

Create a simple PKI on Ubuntu/Debian. Mostly based on [PKI-Tutorial](http://pki-tutorial.readthedocs.org/en/latest/simple/index.html)


## Requirements

Some basic knowledge of openssl and PKI stuff.

## Role Variables

Certificate subject:

	simplepki_domainComponent_tld: "com"
	simplepki_domainComponent_domain: "example"
	simplepki_organizationName: "Example Company Inc"

Servers certificate requests:

	simplepki_server_certs:
	- { fqdn: 'example.com' }
	- { fqdn: 'anothor.com', altnames: ['sub.another.com','mydomain.com']}

User certificate requests:

	simplepki_user_certs:
	- { username: 'fred', fullname: 'Fred Flintstone', email: 'fred@example.com' }
	- { username: 'john', fullname: 'John Example', email: 'john@example.com' }

Revoke certificates:

	simplepki_revocation_list:
	- fred
	- anothor.com

## Create server certs only

	ansible-playbook playbook.yml --tags=servercert
	
## Renew certificates from command-line

Pass extra variable `simplepki_renew_certificates`. This variable should only be passed as command line argument.

	ansible-playbook --extra-vars '{"simplepki_renew_certificates": ["fred","john"]}'

## Revoke certificates from command-line

Pass extra variable `simplepki_revocation_list`.

	ansible-playbook --extra-vars '{"simplepki_revocation_list": ["fred","john"]}'
	
## Dependencies

None

## Example Playbook

    - hosts: pki
      roles:
         - { role: netzwirt.simple-pki }



## Revocation cheat-sheet

Revoke a certificate:

List of valid revocation reasons: 
  - unspecified
  - keyCompromise
  - CACompromise
  - affiliationChanged
  - superseded
  - cessationOfOperation
  - certificateHold

    openssl ca -config etc/signing-ca.conf -revoke certs/fred.sha256.2048.crt -crl_reason unspecified

Create CRL:

    openssl ca -gencrl -config etc/signing-ca.conf -out crl/signing-ca.crl

Check certificate without crl:

    openssl verify -verbose -CAfile ca/chained-ca.sha256.2048.crt certs/fred.sha256.2048.crt

Check certificate with crl:

    openssl verify -crl_check_all -verbose -CAfile ca/chained-ca.sha256.2048.crt \
             -CRLfile crl/signing-ca.crl  certs/fred.sha256.2048.crt

# License

BSD

# Author Information

[netzwirt](https://github.com/netzwirt)
