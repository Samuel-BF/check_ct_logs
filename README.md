# check_ct_logs

This script compares known certificates (stored locally) and registered certificates
logged in public Certificate Transparency logs. It uses
[certspotter API](https://certspotter.com) and can retrieve certificates directly
from host when it finds a registered certificate not already known.

Output and exit follow [Nagios plugins development guidelines](https://nagios-plugins.org/doc/guidelines.html),
making it suitable for integration in monitoring software such as
[Nagios](https://www.nagios.org/), [icinga](https://icinga.com/),
[shinken](http://www.shinken-monitoring.org/), ...

Syntax of this script mainly inspired by
[check_ssl_cert](https://github.com/matteocorti/check_ssl_cert), by Matteo Corti.

This script is free software, licensed as [GPLv3](LICENSE).

# Usage

```
Usage: check_ct_logs -H domain -d certificate_directory [-a API_KEY -g -A "1.2.3.4/example.com test.example.com" -i -v -V -h]

This script compares known certificates (stored locally) and registered certificates
logged in public Certificate Transparency logs. It uses certspotter API, from
https://certspotter.com

Arguments:
        -H, --hostname                  domain name to check
        -d, --certificate-directory     where to find known certificates (PEM encoded)

Options:
        -h, --help                      print this information and exits
        -a, --api-key                   CertSpotter API key. Needed if you do
                                        checks on a regular basis. Get one on :
                                        https://sslmate.com/signup?for=certspotter_api
        -c, --certspotter               Certspotter API URL. Defaults to
                                        https://api.certspotter.com/v1/issuances
                                        But you can change to your instance if you run
                                        certspotter locally
                                        (see https://github.com/SSLMate/certspotter )
        -i, --include-subdomains        if set, the include subdomains option is set
                                        this will search for the hostname and all
                                        subdomains
        -g, --get-from-host             if set, tries to contact host on port 443 to
                                        retrieve certificate from there if there is a
                                        registered certificate not known locally.
        -A, --addresses                 IP or DNS addresses to contact to retrieve
                                        certificate when '-g' is set. If not set, use
                                        argument from -H. For each address, server name
                                        can be specified after '/' : IP/server_name.
        -v, --verbose                   verbose output (can be specified more than once)
        -V, --version                   print script version and exit

Examples:
 check_ct_logs -H test.example.com -d .
 check_ct_logs -H example.com -d . -i -g -A "example.com 10.0.0.5/dev.example.com 10.0.0.8/dev.example.com"
```

# Install

## Dependencies

 - openssl
 - curl
 - jq

## Setup

Just put this script along your other checks, and put known certificates in a
directory that your monitoring software can read. If you use the `--get-from-host`
option, the monitoring software should also be allowed to write to this directory.

If you plan to use it for monitoring, you should
[get an API key](https://sslmate.com/signup) for cert spotter (free up to 1000
queries / hour)

## CRITICAL : what to do ?

If the script answers that it found a certificate in certificate transparency logs
that you didn't expect, try :

- running the same script with `-v` option to know which certificate is missing or
  with `-v -v` to print debug output (debugging certificate download)
- search on [crt.sh](https://crt.sh/?a=1) the certificate (check_ct_logs gives you
  the field `SHA-256(SubjectPublicKeyInfo)`
- investigate : who delivered this certificate ? Who got it ? Should you revoke it ?

# Bugs

Please report bugs to https://github.com/Samuel-BF/check_ct_logs
