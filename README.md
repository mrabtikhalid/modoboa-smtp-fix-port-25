
# Clear the file 
```
echo "" > /etc/postfix/main.cf
```

# Open the file 
```
root@modoboa:~# vi /etc/postfix/main.cf
```

# just copy the rest of file and replace

```
This file was automatically installed on 2022-11-25T14:14:13.022679
inet_interfaces = loopback-only
inet_protocols = all
myhostname = mail.khalidmrabti.com
myorigin = $myhostname
mydestination = $myhostname
mynetworks = 127.0.0.0/8 [::1]/128
smtpd_banner = $myhostname ESMTP
biff = no
unknown_local_recipient_reject_code = 550
unverified_recipient_reject_code = 550

appending .domain is the MUA's job.
append_dot_mydomain = no

readme_directory = no

mailbox_size_limit = 0
message_size_limit = 11534336
recipient_delimiter = +

alias_maps = hash:/etc/aliases
alias_database = hash:/etc/aliases

Proxy maps
proxy_read_maps =
proxy:unix:passwd.byname
proxy:pgsql:/etc/postfix/sql-domains.cf
proxy:pgsql:/etc/postfix/sql-domain-aliases.cf
proxy:pgsql:/etc/postfix/sql-aliases.cf
proxy:pgsql:/etc/postfix/sql-relaydomains.cf
proxy:pgsql:/etc/postfix/sql-maintain.cf
proxy:pgsql:/etc/postfix/sql-relay-recipient-verification.cf
proxy:pgsql:/etc/postfix/sql-sender-login-map.cf
proxy:pgsql:/etc/postfix/sql-spliteddomains-transport.cf
proxy:pgsql:/etc/postfix/sql-transport.cf

TLS settings
smtpd_use_tls = yes
smtpd_tls_auth_only = no
smtpd_tls_CApath = /etc/ssl/certs
#smtpd_tls_key_file = /etc/ssl/private/mail.khalidmrabti.com.key
smtpd_tls_key_file = /captain/data/letencrypt/etc/live/mail.khalidmrabti.com/privkey.pem
#smtpd_tls_cert_file = /etc/ssl/certs/mail.khalidmrabti.com.cert
smtpd_tls_cert_file = /captain/data/letencrypt/etc/live/mail.khalidmrabti.com/cert.pem
smtpd_tls_dh1024_param_file = ${config_directory}/dh2048.pem
smtpd_tls_loglevel = 1
smtpd_tls_session_cache_database = btree:$data_directory/smtpd_tls_session_cache
smtpd_tls_security_level = may
smtpd_tls_received_header = yes

Disallow SSLv2 and SSLv3, only accept secure ciphers
smtpd_tls_protocols = !SSLv2, !SSLv3
smtpd_tls_mandatory_protocols = !SSLv2, !SSLv3
smtpd_tls_mandatory_ciphers = high
smtpd_tls_mandatory_exclude_ciphers = aNULL, MD5 , DES, ADH, RC4, PSD, SRP, 3DES, eNULL
smtpd_tls_exclude_ciphers = aNULL, MD5 , DES, ADH, RC4, PSD, SRP, 3DES, eNULL

Enable elliptic curve cryptography
smtpd_tls_eecdh_grade = strong

Use TLS if this is supported by the remote SMTP server, otherwise use plaintext.
smtp_tls_CApath = /etc/ssl/certs
smtp_tls_security_level = may
smtp_tls_loglevel = 1
smtp_tls_exclude_ciphers = EXPORT, LOW

Virtual transport settings
virtual_transport = lmtp:unix:private/dovecot-lmtp

virtual_mailbox_domains = proxy:pgsql:/etc/postfix/sql-domains.cf
virtual_alias_domains = proxy:pgsql:/etc/postfix/sql-domain-aliases.cf
virtual_alias_maps =
proxy:pgsql:/etc/postfix/sql-aliases.cf

Relay domains
relay_domains =
proxy:pgsql:/etc/postfix/sql-relaydomains.cf
transport_maps =
proxy:pgsql:/etc/postfix/sql-transport.cf
proxy:pgsql:/etc/postfix/sql-spliteddomains-transport.cf

SASL authentication through Dovecot
smtpd_sasl_type = dovecot
smtpd_sasl_path = private/auth
smtpd_sasl_auth_enable = yes
broken_sasl_auth_clients = yes
smtpd_sasl_security_options = noanonymous

SMTP session policies
We require HELO to check it later
smtpd_helo_required = yes

We do not let others find out which recipients are valid
disable_vrfy_command = yes

MTA to MTA communication on Port 25. We expect (!) the other party to
specify messages as required by RFC 821.
strict_rfc821_envelopes = yes

Verify cache setup
address_verify_map = proxy:btree:$data_directory/verify_cache

proxy_write_maps =
$smtp_sasl_auth_cache_name
$lmtp_sasl_auth_cache_name
$address_verify_map

OpenDKIM setup
smtpd_milters = inet:127.0.0.1:12345
non_smtpd_milters = inet:127.0.0.1:12345
milter_default_action = accept
milter_content_timeout = 30s

List of authorized senders
smtpd_sender_login_maps =
proxy:pgsql:/etc/postfix/sql-sender-login-map.cf

# Allow connections from trusted networks only.
#smtpd_client_restrictions = permit_mynetworks, reject

# Don't talk to mail systems that don't know their own hostname.
# With Postfix < 2.3, specify reject_unknown_hostname.
smtpd_helo_restrictions = reject_unknown_helo_hostname

# Don't accept mail from domains that don't exist.
smtpd_sender_restrictions = reject_unknown_sender_domain

# Spam control: exclude local clients and authenticated clients
# from DNSBL lookups.
smtpd_recipient_restrictions = permit_mynetworks,
permit_sasl_authenticated,
# reject_unauth_destination is not needed here if the mail
# relay policy is specified under smtpd_relay_restrictions
# (available with Postfix 2.10 and later).
reject_unauth_destination
reject_rbl_client zen.spamhaus.org,
reject_rhsbl_reverse_client dbl.spamhaus.org,
reject_rhsbl_helo dbl.spamhaus.org,
reject_rhsbl_sender dbl.spamhaus.org
# Relay control (Postfix 2.10 and later): local clients and
# authenticated clients may specify any destination domain.
smtpd_relay_restrictions = permit_mynetworks,
permit_sasl_authenticated,
#reject_unauth_destination

# Block clients that speak too early.
#smtpd_data_restrictions = reject_unauth_pipelining

# Enforce mail volume quota via policy service callouts.
#smtpd_end_of_data_restrictions = check_policy_service unix:private/policy

#^^^^^^îRejecting outside new conf

Postcreen settings
postscreen_access_list =
permit_mynetworks
cidr:/etc/postfix/postscreen_spf_whitelist.cidr
postscreen_blacklist_action = enforce

Use some DNSBL
postscreen_dnsbl_sites =
zen.spamhaus.org=127.0.0.[2..11]3
bl.spameatingmonkey.net=127.0.0.22
bl.spamcop.net=127.0.0.2
dnsbl.sorbs.net=127.0.0.[2..15]
postscreen_dnsbl_threshold = 3
postscreen_dnsbl_action = enforce

postscreen_greet_banner = Welcome, please wait...
postscreen_greet_action = enforce

postscreen_pipelining_enable = yes
postscreen_pipelining_action = enforce

postscreen_non_smtp_command_enable = yes
postscreen_non_smtp_command_action = enforce

postscreen_bare_newline_enable = yes
postscreen_bare_newline_action = enforce
```

# press `esc` then `:x!` then `ENTER`
