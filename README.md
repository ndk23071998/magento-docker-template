## Setup

### Automated Setup (New Project)

```bash
# Create your project directory then go into it:
mkdir -p ~/Sites/magento
cd ~/Sites/magento

# Run this automated one-liner from the directory you want to install your project.
curl -s https://raw.githubusercontent.com/ndk23071998/magento-docker-template/master/template | bash -s -- magento.dev 2.4.6 community
```

The `magento.test` above defines the hostname to use, and the `2.4.6` defines the Magento version to install. Note that since we need a write to `/etc/hosts` for DNS resolution, you will be prompted for your system password during setup.

After the one-liner above completes running, you should be able to access your site at `https://magento.dev`.

#### Install sample data

After the above installation is complete, run the following lines to install sample data:

```bash
bin/magento sampledata:deploy
bin/magento setup:upgrade
```

### Manual Setup

Same result as the one-liner above. Just replace `magento.test` references with the hostname that you wish to use.

#### New Projects

```bash
# Create your project directory then go into it:
mkdir -p ~/Sites/magento
cd ~/Sites/magento

# Download the Docker Compose template:
curl -s https://raw.githubusercontent.com/ndk23071998/magento-docker-template/master/template | bash

# Download the version of Magento you want to use with:
bin/download 2.4.6 community
# You can specify the version and type (community, enterprise, mageos, mageos-nightly, mageos-mirror, mageos-hypernode-mirror, or mageos-maxcluster-mirror).
# The mageos type is an alias for mageos-mirror.
# If no arguments are passed, "2.4.6" and "community" are the default values used.

# or for Magento core development:
# bin/start --no-dev
# bin/setup-composer-auth
# bin/cli git clone git@github.com:magento/magento2.git .
# bin/cli git checkout 2.4-develop
# bin/composer install

# Want to install Magento <2.4.6? In bin/setup-install, replace the lines:
#  --elasticsearch-host="$ES_HOST" \
#  --elasticsearch-port="$ES_PORT" \
#  --opensearch-host="$OPENSEARCH_HOST" \
#  --opensearch-port="$OPENSEARCH_PORT" \
#  --search-engine=opensearch \
# with:
#  --elasticsearch-host="$OPENSEARCH_HOST" \
#  --elasticsearch-port="$OPENSEARCH_PORT" \
#  --search-engine=elasticsearch7 \

# Run the setup installer for Magento:
bin/setup magento.dev

open https://magento.dev
```

#### Existing Projects

```bash

# Create your project directory then go into it:
mkdir -p ~/Sites/magento
cd ~/Sites/magento

# Download the Docker Compose template:
curl -s https://raw.githubusercontent.com/ndk23071998/magento-docker-template/master/template | bash

# Replace with existing source code of your existing Magento instance:
git clone git@github.com:myrepo.git src

# Start some containers, copy files to them and then restart the containers:
bin/start --no-dev
bin/copytocontainer --all ## Initial copy will take a few minutes...

# If your vendor directory was empty, populate it with:
bin/composer install

# Import existing database:
bin/mysql < ../existing/magento.sql

# Getting an "Access denied, you need (at least one of) the SUPER privilege(s) for this operation." message when running one of the above lines? Try running it as root with:
bin/clinotty mysql -hdb -uroot -pmagento magento < src/backup.sql

# You can also remove the DEFINER lines from the MySQL backup file with:
sed 's/\sDEFINER=`[^`]*`@`[^`]*`//g' -i src/backup.sql

# Update database connection details to use the above Docker MySQL credentials:
# Also note: creds for the MySQL server are defined at startup from env/db.env
# vi src/app/etc/env.php

# Create a DNS host entry and setup Magento base url
bin/setup-domain yoursite.dev

bin/restart

open https://yoursite.dev

# Copyright belong to https://github.com/markshust/docker-magento