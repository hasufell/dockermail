Dockermail
==========

A secure, minimal-configuration mail service in docker containers.
This repository is tailored to small private servers, where you own a domain and want to host your own mail.

The setup is modular and so far has (more to come, feel free to contribute :)

* `core` -  base SMTP and IMAP server
* `opendkim` - adds DKIM signing service to the core

Please see the README in each folder for more information on each image.

### SPAM
Although OpenDKIM is optional, I highly recommended you use it unless you want your mail to end up in someone's spam folder. See the README in `opendkim` folder for more info on setting it up.

You should also add PTR record to your IP (aka Reverse DNS) which is done by your server provider.
And finally, generate and add an SPF record to your domain, search for instructions on this - there are a few generator site around and the setup steps depend on your domain name provider.

### Compose
Assuming you follow the instructions in the accompanying READMEs to set up both containers, you should just be able to run

		docker-compose up

and it will spin up both container and link them together, easy!


### Coming soon
* JSON based config instead of current collection of flat files.
* Images on DockerHub
* Testing

### TODO
* gentoo support for opendkim
