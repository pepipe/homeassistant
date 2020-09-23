<link>https://www.duckdns.org/domains</link>

login with gmail 


https://www.home-assistant.io/blog/2015/12/13/setup-encryption-using-lets-encrypt/

./certbot-auto renew --quiet --no-self-upgrade --standalone \
                     --standalone-supported-challenges http-01


I'm using HTTPS so we need a cert. I'm using dehydrated ([check guide](https://www.splitbrain.org/blog/2017-08/10-homeassistant_duckdns_letsencrypt)).

Just run the code in dehydrated folder: $./dehydrated -c
