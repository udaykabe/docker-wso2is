This project provides the WSO2 Identity Server as a docker container.

	docker run --rm --name myldapserver -d -p 389:10389 -p 80:9763 exolyte/wso2is


## Testing LDAP on WSO2IS

The 'ldapsearch' command provides a command-line ability to search an LDAP directory tree.  The package 'ldap-utils' must be installed to get access to the ldapsearch command.  The following three commands are equivalent (assuming the LDAP server is listening on port 389, WSO2IS listens on 10389), and display the root DSE (Directory Service Agent - Specific Entry) on WSO2IS's embedded Apache LDAP Directory Server; on WSO2IS, the default password for the 'admin' user is 'admin':

	ldapsearch -x -w "admin" -D 'uid=admin,ou=system' -b "" -s base
	ldapsearch -x -w "admin" -D 'uid=admin,ou=system' -b "" -s base '(objectclass=*)'
	ldapsearch -H ldap://localhost:389 -x -W -D 'uid=admin,ou=system' -b "" -s base (the -W option requests an interactive password prompt)
	
result in the output below:

	# extended LDIF
	#
	# LDAPv3
	# base <> with scope baseObject
	# filter: (objectclass=*)
	# requesting: ALL
	#
	
	#
	dn:
	objectClass: extensibleObject
	objectClass: top
	
	# search result
	search: 2
	result: 0 Success
	
	# numResponses: 2
	# numEntries: 1

The following command with an objectclass=* wild card filter:

	ldapsearch -x -w "admin" -D 'uid=admin,ou=system' -b "" -s base '(objectclass=*)' +

results in the following output:

	# extended LDIF
	#
	# LDAPv3
	# base <> with scope baseObject
	# filter: (objectclass=*)
	# requesting: +
	#
	
	#
	dn:
	supportedFeatures: 1.3.6.1.4.1.4203.1.5.1
	namingContexts: ou=system
	namingContexts: ou=schema
	namingContexts: dc=WSO2,dc=ORG
	supportedLDAPVersion: 3
	vendorName: Apache Software Foundation
	subschemaSubentry: cn=schema
	vendorVersion: 1.5.7
	supportedSASLMechanisms: PLAIN
	supportedSASLMechanisms: GSSAPI
	supportedSASLMechanisms: NTLM
	supportedSASLMechanisms: GSS-SPNEGO
	supportedSASLMechanisms: CRAM-MD5
	supportedSASLMechanisms: DIGEST-MD5
	supportedExtension: 1.3.6.1.4.1.1466.20036
	supportedExtension: 1.3.6.1.4.1.1466.20037
	supportedExtension: 1.3.6.1.4.1.18060.0.1.7
	supportedExtension: 1.3.6.1.4.1.18060.0.1.6
	supportedControl: 2.16.840.1.113730.3.4.3
	supportedControl: 2.16.840.1.113730.3.4.7
	supportedControl: 1.3.6.1.4.1.4203.1.10.1
	supportedControl: 2.16.840.1.113730.3.4.2
	supportedControl: 1.3.6.1.4.1.18060.0.0.1
	supportedControl: 1.2.840.113556.1.4.319
	supportedControl: 1.3.6.1.4.1.4203.1.9.1.3
	supportedControl: 1.3.6.1.4.1.4203.1.9.1.4
	supportedControl: 1.3.6.1.4.1.4203.1.9.1.1
	supportedControl: 1.3.6.1.4.1.4203.1.9.1.2
	
	# search result
	search: 2
	result: 0 Success
	
	# numResponses: 2
	# numEntries: 1


The following command with an objectclass=* wild card filter:

	ldapsearch -x -w "admin" -D 'uid=admin,ou=system' -b "ou=Users,dc=wso2,dc=org" '(objectclass=*)'

results in:

	# extended LDIF
	#
	# LDAPv3
	# base <ou=Users,dc=wso2,dc=org> with scope subtree
	# filter: (objectclass=*)
	# requesting: ALL
	#
	
	# Users, WSO2.ORG
	dn: ou=Users,dc=WSO2,dc=ORG
	objectClass: organizationalUnit
	objectClass: top
	ou: Users
	
	# admin, Users, WSO2.ORG
	dn: uid=admin,ou=Users,dc=WSO2,dc=ORG
	krb5KeyVersionNumber: 0
	userPassword:: e1NIQX0wRFBpS3VOSXJyVm1EOElVQ3V3MWhReE5xWmM9
	krb5Key:: MBmgAwIBF6ESBBB8tcgf8UdxAdQHKP/0oST4
	krb5Key:: MBGgAwIBA6EKBAiroVFbqMJhhg==
	krb5Key:: MCGgAwIBEKEaBBiGPWJe+yxFW0p604BSeUUlbQjLB784W7k=
	krb5Key:: MBmgAwIBEaESBBC7Dl59V4efJZ+eBBkmAIBr
	krb5Key:: MCmgAwIBEqEiBCCE390qLNCnXB5wXg7EnIN7E3ySpAXdIN9MlioQ6rhnEQ==
	sn: admin
	mail: admin@wso2.com
	givenName: admin
	krb5PrincipalName: admin/carbon.super@WSO2.ORG
	uid: admin
	objectClass: wso2Person
	objectClass: inetOrgPerson
	objectClass: organizationalPerson
	objectClass: person
	objectClass: scimPerson
	objectClass: krb5Principal
	objectClass: krb5KDCEntry
	objectClass: identityPerson
	objectClass: top
	cn: admin
	
	# krbtgt, Users, WSO2.ORG
	dn: uid=krbtgt,ou=Users,dc=WSO2,dc=ORG
	krb5KeyVersionNumber: 0
	userPassword:: c2VjcmV0
	krb5Key:: MBmgAwIBF6ESBBCHjYAUYGzaKWd6RO+hNT/H
	krb5Key:: MBGgAwIBA6EKBAiitlH7bfcmHA==
	krb5Key:: MCGgAwIBEKEaBBgs31GwAW4m03kfzhO1xGTv2mhATDS1MgQ=
	krb5Key:: MBmgAwIBEaESBBBrwbzmKrR4TUZNJDQ7U4hR
	krb5Key:: MCmgAwIBEqEiBCA1v2p39gtFUO9CCI4c4Ou5fPa9QlDldnhPtDrPF2eV+w==
	sn: Service
	krb5PrincipalName: krbtgt/WSO2.ORG@WSO2.ORG
	uid: krbtgt
	objectClass: person
	objectClass: inetOrgPerson
	objectClass: organizationalPerson
	objectClass: krb5Principal
	objectClass: krb5KDCEntry
	objectClass: top
	cn: KDC Service
	
	# ldap, Users, WSO2.ORG
	dn: uid=ldap,ou=Users,dc=WSO2,dc=ORG
	krb5KeyVersionNumber: 0
	userPassword:: cmFuZGFsbA==
	krb5Key:: MBmgAwIBF6ESBBAb6C6vsFSgv/x3J88x0Ojn
	krb5Key:: MBGgAwIBA6EKBAgNRVSYm+qYAQ==
	krb5Key:: MCGgAwIBEKEaBBhK8a6RI4zO8n8WFYwj1cLcGZiA1nUN7FQ=
	krb5Key:: MBmgAwIBEaESBBDWSp67ax4pzEwQ5wrcwud/
	krb5Key:: MCmgAwIBEqEiBCAtFiXjNsguczpUOLsRiX169v7wjegSF89U7kSpxXPVSw==
	sn: Service
	krb5PrincipalName: ldap/localhost@WSO2.ORG
	uid: ldap
	objectClass: person
	objectClass: inetOrgPerson
	objectClass: organizationalPerson
	objectClass: krb5Principal
	objectClass: krb5KDCEntry
	objectClass: top
	cn: LDAP Service
	
	# search result
	search: 2
	result: 0 Success
	
	# numResponses: 5
	# numEntries: 4

Adding a new user with a username of 'johndoe' using the WSO2IS Management Console, and then searching for that username with the following command:

	ldapsearch -x -w "admin" -D 'uid=admin,ou=system' -b "ou=Users,dc=wso2,dc=org" '(&(objectclass=inetOrgPerson) (uid=johndoe))'

will return the following information for 'John Doe':

	# extended LDIF
	#
	# LDAPv3
	# base <ou=Users,dc=wso2,dc=org> with scope subtree
	# filter: (&(objectclass=inetOrgPerson) (cn=johndoe))
	# requesting: ALL
	#

	# johndoe, Users, wso2.org
	dn: uid=johndoe,ou=Users,dc=wso2,dc=org
	mobile: 8885551212
	country: United States
	userPassword:: V2UhY29tZT=
	lastModifiedDate: 2018-01-28T23:53:48
	sn: Doe
	scimId: fc8f7cd4-ef01-4f33-96aa-18335e76ebff
	mail: johndoe@example.com
	givenName: John
	uid: johndoe
	telephoneNumber: 8005551212
	objectClass: wso2Person
	objectClass: inetOrgPerson
	objectClass: organizationalPerson
	objectClass: person
	objectClass: scimPerson
	objectClass: identityPerson
	objectClass: top
	createdDate: 2018-01-28T23:53:00
	organizationName: Acme Technology
	organizationName: Inc.
	cn: johndoe

	# search result
	search: 2
	result: 0 Success

	# numResponses: 2
	# numEntries: 1

