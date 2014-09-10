## SANDBOX SETUP (Ubuntu) - WeedMaps

This doc is a starting point for packages needed to run Weedmaps in a "sandbox environment". It is based off of [local Server Setup Doc](https://github.com/GhostGroup/weedmaps/wiki/Local-development-server-setup)

#### Ubuntu
```
apt-get install build-essential
apt-get install libssl-dev libcurl4-openssl-dev libreadline-dev libffi-dev libxslt1-dev libyaml-dev
sudo apt-get install git-core
```

#### Rbenv install
```
git clone https://github.com/sstephenson/rbenv.git ~/.rbenv
echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bashrc
echo 'eval "$(rbenv init -)"' >> ~/.bashrc
source ~/.bashrc
```
To test that rbenv has been installed 

```
$ type rbenv
rbenv is a function
```

#### install ruby-build and gem-rehash
```
git clone https://github.com/sstephenson/ruby-build.git ~/.rbenv/plugins/ruby-build
git clone https://github.com/sstephenson/rbenv-gem-rehash.git ~/.rbenv/plugins/rbenv-gem-rehash
```

NOTE: build failed for patch level 353, needed to patch ruby version and install with: 
```
curl -fsSL https://gist.githubusercontent.com/riocampos/b2669b26016207224f06/raw/readline.patch | CONFIGURE_OPTS='--disable-install-rdoc' ~/.rbenv/bin/rbenv install --patch 2.0.0-p353
```

### install bundler
```
gem install bundler
```

#### installing postgresql 9.3
```
apt-get install postgresql-server-9.3
apt-get install postgresql-server-dev-9.3
apt-get install libgeos++-dev
```

#### installing postgis
```
apt-get install postgresql-9.3-postgis-2.1 # Geographic objects support for PostgreSQL 9.3
apt-get install postgresql-9.3-postgis-2.1-scripts # PostGIS for PostgreSQL 9.3 -- dummy package
apt-get install postgresql-9.3-postgis-scripts - Geographic objects support for PostgreSQL 9.3 -- scripts
```

#### install elasticsearch

Download the .deb pkg :

```
wget https://download.elasticsearch.org/elasticsearch/elasticsearch/elasticsearch-1.3.2.deb
sudo dpkg -i elasticsearch-1.3.2.deb

Selecting previously unselected package elasticsearch.
(Reading database ... 62552 files and directories currently installed.)
Preparing to unpack elasticsearch-1.3.2.deb ...
Unpacking elasticsearch (1.3.2) ...
Setting up elasticsearch (1.3.2) ...
Adding system user `elasticsearch' (UID 109) ...
Adding new user `elasticsearch' (UID 109) with group `elasticsearch' ...
Not creating home directory `/usr/share/elasticsearch'.
### NOT starting elasticsearch by default on bootup, please execute
 sudo update-rc.d elasticsearch defaults 95 10
### In order to start elasticsearch, execute
 sudo /etc/init.d/elasticsearch start
```

#### Start ES and chech status
```
$ curl http://localhost:9200
```

See notes below if you are having issues.

### install redis and memcached
```
sudo apt-get install redis-server
sudo apt-get install memcached
```
### Install node
```
sudo apt-get install nodejs nodejs-dev
```
# Setup postgres

```
su - postgres
createuser -d -r -s deploy
createdb weedmaps_development -O deploy 
```

# weedmaps app setup
```
git clone git@github.com:GhostGroup/weedmaps.git
bundle install
```
```
bundle exec rake geoip:download_data
```
Copy the config/database.yml.example file to your config/database.yml
```
cp $APP_PATH/config/database.yml.example $APP_PATH/config/database.yml
```

```
bundle exec rake geoip:download_data
```

Import the database (This may take a bit)
```
rake db:import 


2014-09-10T18:49:27Z 28318 TID-oxmnzqc28 INFO: Sidekiq client with redis options {:url=>"http://localhost:6379"}
Downloading weedmaps_anon_exclude.pgsql-20140910 to /home/ubuntu/weedmaps/weedmaps.git/db/dump/weedmaps_anon_exclude.pgsql-20140910...
^@Time: 00:01:03 ========                                     118784/610360K 19%  ETA: 00:04:24 1885 KB/s^@Time: 00:02:03 ================                             230400/610360K 37%  ETA: 00:03:24 1873 KB/s^@Time: 00:03:03 =========================                    348160/610360K 57%  ETA: 00:02:19 1902 KB/s^@Time: 00:04:03 =================================            467968/610360K 76%  ETA: 00:01:14 1925 KB/s^@Time: 00:05:03 ==========================================   586752/610360K 96%  ETA: 00:00:12 1936 KB/sTime: 00:05:16 =========================================== 610360/610360K 100% Time: 00:05:16 1931 KB/sec
Clearing schema 'public' in development...
Setting up PostGIS in development...
Clearing schema 'public' in test...
Setting up PostGIS in test...
Importing /home/ubuntu/weedmaps/weedmaps.git/db/dump/weedmaps_anon_exclude.pgsql-20140910...
^@^@^@^@Imported. Database size: 4224 MB
==  AddLonLatToAddresses: migrating ===========================================
-- add_column(:addresses, :lon_lat, :point, {:srid=>3785})
   -> 0.0345s
-- add_index(:addresses, :lon_lat, {:spatial=>true})
   -> 0.0501s
==  AddLonLatToAddresses: migrated (0.0848s) ==================================

==  SetLonLatToLongitudeAndLatitude: migrating ================================
-- update("UPDATE addresses\n  SET lon_lat = ST_SetSRID(ST_Point(longitude, latitude), 3785)\n  WHERE longitude IS NOT NULL AND latitude IS NOT NULL\n")
   -> 1.2229s
   -> 23175 rows
==  SetLonLatToLongitudeAndLatitude: migrated (1.2230s) =======================

==  DropAddressbook: migrating ================================================
-- drop_table(:addressbooks)
   -> 0.0054s
==  DropAddressbook: migrated (0.0056s) =======================================

==  DropBadEmailAddresses: migrating ==========================================
-- drop_table(:bad_email_addresses)
   -> 0.0013s
==  DropBadEmailAddresses: migrated (0.0014s) =================================

==  AddAncestryDepthToRegions: migrating ======================================
-- add_column(:regions, :ancestry_depth, :integer, {:default=>0})
   -> 0.1802s
==  AddAncestryDepthToRegions: migrated (0.1803s) =============================

Dispensary      Time: 00:08:38 ===================================== 22103/22103 100% Time: 00:08:3835
Doctor          Time: 00:00:18 ======================================= 1078/1078 100% Time: 00:00:18
Region          Time: 00:00:01 ========================================= 329/329 100% Time: 00:00:01
ZipcodePin      Time: 00:00:01 ======================================= 2120/2120 100% Time: 00:00:01
^@Time: 00:00:00                                                                    1/329 0%  ETA: 00:06:Time: 00:00:59 ============================================================== 329/329 100% Time:
...


Confirm 'rgeo' was installed correctly.

```
bundle exec rails console

pry(main)> RGeo::Geos.supported?
=> true
```
```
node install and node install gulp
npm install
npm install -g gulp
```

index elastisearch data

```
rake elasticsearch:update
```
## NOTES: 

postgresql : 

When rake is run it uses "psql --host localhost" which will always prompt for a password, but since we are relying on pg auth as a local process i had to edit /etc/postgresql/9.3/main/pg_hba.conf and change the security setting for local to "trust". This issue seems to be specifc to Ubuntu PG packages. 

When installing elastisearch with .deb pkg, I had to installed java packages >= 7. 

```
sudo apt-get install openjdk-7-jre
```

You can test ES directly, which will sometimes show more verbose output to troubleshoot

```
/usr/share/elasticsearch/bin/elasticsearch -f
```