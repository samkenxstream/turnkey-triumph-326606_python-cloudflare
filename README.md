# cloudflare


## Credit

This is based on work by _gnowxilef_ found [here](https://github.com/cloudflare-api/python-cloudflare-v4). It has been seriously expanded upon.

## CloudFlare API version 4

The CloudFlare API can be found [here](https://api.cloudflare.com/). Each API call is provided via a similarly named function within the _CloudFlare_ class. A full list is provided below.

## Installation

```
	./setup.py install
```

## Getting Started

A very simple listing of zones within your account; including the IPv6 status of the zone.

```
from CloudFlare import CloudFlare

def main():
	cf = CloudFlare.CloudFlare()
	zones = cf.zones.get(param={'per_page':50})
	for zone in zones:
		zone_name = zone['name']
		zone_id = zone['id']
		settings_ipv6 = cf.zones.settings.ipv6.get(zone_id)
		ipv6_status = settings_ipv6['value']
		settings_ssl = cf.zones.settings.ssl.get(zone_id)
		ssl_status = settings_ssl['value']
		print zone_id, ssl_status, ipv6_status, zone_name

if __name__ == '__main__':
	main()
```

A more complex example follows.

```
	zone_name = 'example.com'

	cf = CloudFlare.CloudFlare()

	# query for the zone name and expect only one value back
	try:
		zones = cf.zones.get(params = {'name':zone_name,'per_page':1})
	except ConnectionError, e:
		# unable to connect to CloudFlare - possibly you're offline
		print '/zones - api call failed'
		exit(1)
	except Exception, e:
		print '/zones - api call failed'
		exit(1)

	# extract the zone_id which is needed to process that zone
	zone = zones[0]
	zone_id = zone['id']

	# request the DNS records from that zone
	try:
		dns_records = cf.zones.dns_records.get(zone_id)
	except Exception, e:
		print '/zones/dns_records - api call failed'
		exit(1)

	# print the results - first the zone name
	print zone_id, zone_name

	# then all the DNS records for that zone
	for dns_record in dns_records:
		r_name = dns_record['name']
		r_type = dns_record['type']
		r_value = dns_record['content']
		r_id = dns_record['id']
		print '\t', r_id, r_name, r_type, r_value


```

## Providing CloudFlare Username and API Key

When you create a _CloudFlare_ class you can pass up to three paramaters.

 * Account email
 * Account API key
 * Optional Debug flag (True/False)

If the account email and API key are not passed when you create the class, then they are retreived from either the users exported shell environment variables or the .cloudflare.cfg or ~/.cloudflare.cfg or ~/.cloudflare/cloudflare.cfg files, in that order.

### Using shell environment variables
```
$ export CF_API_EMAIL='user@example.com'
$ export CF_API_KEY='00000000000000000000000000000000'
$
```

### Using configuration file to store email and keys

```
$ cat ~/.cloudflare/cloudflare.cfg 
[CloudFlare]
email = user@example.com
token = 00000000000000000000000000000000
$
```

## Included example code

The _example_ folder contains many examples in both simple and verbose formats.

## CLI

All API calls can be called from the command line. The command will convert domain names on-the-fly into zone_identifier's.

```
$ cli4 [-h|--help] [-v|--verbose] [-q|--quiet] [--get|--patch|--post|-put|--delete] /command...

```

Sample commands include

 * ```cli4 /user/billing/profile```
 * ```cli4 /user/invites```

 * ```cli4 /zones/:example.com```
 * ```cli4 /zones/:example.com/dnssec```
 * ```cli4 /zones/:example.com/settings/ipv6```
 * ```cli4 --put /zones/:example.com/activation_check```
 * ```cli4 /zones/:example.com/keyless_certificates```

 * ```cli4 /zones/:example.com/analytics/dashboard```

The output from the CLI command is in json format (and human readable).

## Implemented API calls

```
	GET	/user
	PATCH	/user

	GET	/user/billing/history
	GET	/user/billing/profile
	GET	/user/billing/subscriptions/apps
	GET	/user/billing/subscriptions/apps/:identifier
	GET	/user/billing/subscriptions/zones
	GET	/user/billing/subscriptions/zones/:identifier

	GET	/user/firewall/access_rules/rules
	POST	/user/firewall/access_rules/rules
	PATCH	/user/firewall/access_rules/rules/:identifier
	DELETE	/user/firewall/access_rules/rules/:identifier

	GET	/user/organizations
	GET	/user/organizations/:organization_identifier
	DELETE	/user/organizations/:organization_identifier

	GET	/user/invites
	GET	/user/invites/:identifier
	PATCH	/user/invites/:identifier

	GET	/zones
	POST	/zones
	GET	/zones/:zone_identifier
	PATCH	/zones/:zone_identifier
	DELETE	/zones/:zone_identifier

	PUT	/zones/:zone_identifier/activation_check

	GET	/zones/:zone_identifier/analytics/colos
	GET	/zones/:zone_identifier/analytics/dashboard

	GET	/zones/:zone_identifier/available_plans
	GET	/zones/:zone_identifier/available_plans/:identifier

	GET	/zones/:zone_identifier/dns_records
	POST	/zones/:zone_identifier/dns_records
	GET	/zones/:zone_identifier/dns_records/:identifier
	PUT	/zones/:zone_identifier/dns_records/:identifier
	DELETE	/zones/:zone_identifier/dns_records/:identifier

	DELETE	/zones/:zone_identifier/purge_cache

	GET	/zones/:zone_identifier/railguns
	GET	/zones/:zone_identifier/railguns/:identifier
	PATCH	/zones/:zone_identifier/railguns/:identifier
	GET	/zones/:zone_identifier/railguns/:identifier/diagnose

	GET	/zones/:zone_identifier/settings
	PATCH	/zones/:zone_identifier/settings
	GET	/zones/:zone_identifier/settings/advanced_ddos
	GET	/zones/:zone_identifier/settings/always_online
	PATCH	/zones/:zone_identifier/settings/always_online
	GET	/zones/:zone_identifier/settings/browser_cache_ttl
	PATCH	/zones/:zone_identifier/settings/browser_cache_ttl
	GET	/zones/:zone_identifier/settings/browser_check
	PATCH	/zones/:zone_identifier/settings/browser_check
	GET	/zones/:zone_identifier/settings/cache_level
	PATCH	/zones/:zone_identifier/settings/cache_level
	GET	/zones/:zone_identifier/settings/challenge_ttl
	PATCH	/zones/:zone_identifier/settings/challenge_ttl
	GET	/zones/:zone_identifier/settings/development_mode
	PATCH	/zones/:zone_identifier/settings/development_mode
	GET	/zones/:zone_identifier/settings/email_obfuscation
	PATCH	/zones/:zone_identifier/settings/email_obfuscation
	GET	/zones/:zone_identifier/settings/hotlink_protection
	PATCH	/zones/:zone_identifier/settings/hotlink_protection
	GET	/zones/:zone_identifier/settings/ip_geolocation
	PATCH	/zones/:zone_identifier/settings/ip_geolocation
	GET	/zones/:zone_identifier/settings/ipv6
	PATCH	/zones/:zone_identifier/settings/ipv6
	GET	/zones/:zone_identifier/settings/minify
	PATCH	/zones/:zone_identifier/settings/minify
	GET	/zones/:zone_identifier/settings/mirage
	PATCH	/zones/:zone_identifier/settings/mirage
	GET	/zones/:zone_identifier/settings/mobile_redirect
	PATCH	/zones/:zone_identifier/settings/mobile_redirect
	GET	/zones/:zone_identifier/settings/origin_error_page_pass_thru
	PATCH	/zones/:zone_identifier/settings/origin_error_page_pass_thru
	GET	/zones/:zone_identifier/settings/polish
	PATCH	/zones/:zone_identifier/settings/polish
	GET	/zones/:zone_identifier/settings/prefetch_preload
	PATCH	/zones/:zone_identifier/settings/prefetch_preload
	GET	/zones/:zone_identifier/settings/response_buffering
	PATCH	/zones/:zone_identifier/settings/response_buffering
	GET	/zones/:zone_identifier/settings/rocket_loader
	PATCH	/zones/:zone_identifier/settings/rocket_loader
	GET	/zones/:zone_identifier/settings/security_header
	PATCH	/zones/:zone_identifier/settings/security_header
	GET	/zones/:zone_identifier/settings/security_level
	PATCH	/zones/:zone_identifier/settings/security_level
	GET	/zones/:zone_identifier/settings/server_side_exclude
	PATCH	/zones/:zone_identifier/settings/server_side_exclude
	GET	/zones/:zone_identifier/settings/sort_query_string_for_cache
	PATCH	/zones/:zone_identifier/settings/sort_query_string_for_cache
	GET	/zones/:zone_identifier/settings/ssl
	PATCH	/zones/:zone_identifier/settings/ssl
	GET	/zones/:zone_identifier/settings/tls_1_2_only
	PATCH	/zones/:zone_identifier/settings/tls_1_2_only
	GET	/zones/:zone_identifier/settings/tls_client_auth
	PATCH	/zones/:zone_identifier/settings/tls_client_auth
	GET	/zones/:zone_identifier/settings/true_client_ip_header
	PATCH	/zones/:zone_identifier/settings/true_client_ip_header
	GET	/zones/:zone_identifier/settings/waf
	PATCH	/zones/:zone_identifier/settings/waf

	GET	/railguns
	POST	/railguns
	GET	/railguns/:identifier
	PATCH	/railguns/:identifier
	DELETE	/railguns/:identifier
	GET	/railguns/:identifier/zones

	GET	/zones/:zone_identifier/firewall/access_rules/rules
	POST	/zones/:zone_identifier/firewall/access_rules/rules
	PATCH	/zones/:zone_identifier/firewall/access_rules/rules/:identifier
	DELETE	/zones/:zone_identifier/firewall/access_rules/rules/:identifier

	GET	/zones/:zone_identifier/firewall/waf/packages/:package_identifier/rules
	GET	/zones/:zone_identifier/firewall/waf/packages/:package_identifier/rules/:identifier
	PATCH	/zones/:zone_identifier/firewall/waf/packages/:package_identifier/rules/:identifier

	GET	/zones/:zone_identifier/custom_certificates
	POST	/zones/:zone_identifier/custom_certificates
	GET	/zones/:zone_identifier/custom_certificates/:identifier
	PATCH	/zones/:zone_identifier/custom_certificates/:identifier
	DELETE	/zones/:zone_identifier/custom_certificates/:identifier
	PUT	/zones/:zone_identifier/custom_certificates/prioritize

	GET	/zones/:zone_identifier/custom_pages
	GET	/zones/:zone_identifier/custom_pages/:identifier
	PUT	/zones/:zone_identifier/custom_pages/:identifier

	GET	/zones/:zone_identifier/firewall/waf/packages
	GET	/zones/:zone_identifier/firewall/waf/packages/:identifier
	PATCH	/zones/:zone_identifier/firewall/waf/packages/:identifier
	GET	/zones/:zone_identifier/firewall/waf/packages/:package_identifier/groups
	GET	/zones/:zone_identifier/firewall/waf/packages/:package_identifier/groups/:identifier
	PATCH	/zones/:zone_identifier/firewall/waf/packages/:package_identifier/groups/:identifier

	GET	/zones/:zone_identifier/keyless_certificates
	POST	/zones/:zone_identifier/keyless_certificates
	GET	/zones/:zone_identifier/keyless_certificates/:identifier
	PATCH	/zones/:zone_identifier/keyless_certificates/:identifier
	DELETE	/zones/:zone_identifier/keyless_certificates/:identifier

	GET	/zones/:zone_identifier/pagerules
	POST	/zones/:zone_identifier/pagerules
	GET	/zones/:zone_identifier/pagerules/:identifier
	PUT	/zones/:zone_identifier/pagerules/:identifier
	PATCH	/zones/:zone_identifier/pagerules/:identifier
	DELETE	/zones/:zone_identifier/pagerules/:identifier

	GET	/organizations/:organization_identifier
	PATCH	/organizations/:organization_identifier

	GET	/organizations/:organization_identifier/members
	GET	/organizations/:organization_identifier/members/:identifier
	PATCH	/organizations/:organization_identifier/members/:identifier
	DELETE	/organizations/:organization_identifier/members/:identifier

	GET	/organizations/:organization_identifier/invites
	POST	/organizations/:organization_identifier/invites
	GET	/organizations/:organization_identifier/invites/:identifier
	PATCH	/organizations/:organization_identifier/invites/:identifier
	DELETE	/organizations/:organization_identifier/invites/:identifier

	GET	/organizations/:organization_identifier/roles
	GET	/organizations/:organization_identifier/roles/:identifier

	GET	/organizations/:organization_identifier/firewall/access_rules/rules
	POST	/organizations/:organization_identifier/firewall/access_rules/rules
	PATCH	/organizations/:organization_identifier/firewall/access_rules/rules/:identifier
	DELETE	/organizations/:organization_identifier/firewall/access_rules/rules/:identifier

	GET	/organizations/:organization_identifier/railguns
	POST	/organizations/:organization_identifier/railguns
	GET	/organizations/:organization_identifier/railguns/:identifier
	GET	/organizations/:organization_identifier/railguns/:identifier/zones
	PATCH	/organizations/:organization_identifier/railguns/:identifier
	DELETE	/organizations/:organization_identifier/railguns/:identifier

	GET	/certificates
	POST	/certificates
	GET	/certificates/:identifier
	DELETE	/certificates/:identifier

	GET	/user/virtual_dns
	POST	/user/virtual_dns
	GET	/user/virtual_dns/:identifier
	PATCH	/user/virtual_dns/:identifier
	DELETE	/user/virtual_dns/:identifier

	GET	/organizations/:organization_identifier/virtual_dns
	POST	/organizations/:organization_identifier/virtual_dns
	GET	/organizations/:organization_identifier/virtual_dns/:identifier
	PATCH	/organizations/:organization_identifier/virtual_dns/:identifier
	DELETE	/organizations/:organization_identifier/virtual_dns/:identifier

	GET	/ips
```