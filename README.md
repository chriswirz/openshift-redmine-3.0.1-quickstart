<h1>Redmine 3.0.1 for Openshift</h1>
<p>The following procedure will create a redmine instance - and was the procedure used to create this repo after a successful deployment.</p>
<pre>
# The Openshift application must be configured as follows:
# Ruby 1.9 or 2.0 (no scaling)
# MySql 5.1

# Begin the installation by navigating to the app runtime directory
cd ~/app-root/runtime/repo/
# download redmine
wget http://www.redmine.org/releases/redmine-3.0.1.tar.gz
tar xvzf redmine-3.0.1.tar.gz
rm redmine-3.0.1.tar.gz

# extract it to its new environment
rm -rf public
rm -rf tmp
mv redmine-3.0.1/* ~/app-root/runtime/repo/
rm -rf redmine-3.0.1*

# make sure rails is installed
gem install rails -v '4.2.1'
gem install bundler -no-ri -no-rdoc
gem install mysql2 -v '0.3.18'

# get the configuration files
cd ~/app-root/runtime/repo/config
wget —no-check-certificate https://raw.githubusercontent.com/chriswirz/openshift-redmine-3.0.1-quickstart/master/config/database.yml
wget —no-check-certificate https://raw.githubusercontent.com/chriswirz/openshift-redmine-3.0.1-quickstart/master/config/configuration.yml
cd ~/app-root/runtime/repo/

# bundle install
bundle install --no-deployment

# populate the database
rake generate_secret_token
RAILS_ENV=production rake db:migrate
RAILS_ENV=production rake redmine:load_default_data

# restart the app/gear
gear stop
gear start
</pre>

<p>Once the gear is functioning properly, add the contents to the the repository.  You must have the gear's ssh key added to your remote repository's (or application's) allowed key collection.  Here is how to do that:</p>
<pre>
git config --global user.name "you"
git config --global user.email "you@email.com"
ssh-keygen -t rsa -C "you@email.com"
cat ~/.ssh/id_rsa.pub
</pre>
<p>Now that the SSH key is added, you can push the code to a repo.  This example just shows you how to push to your gear's repo, but I have also tested this with github (the repo you're looking at now).</p>
<pre>
cd ~/app-root/runtime/repo/
git init
git add .
git commit -m 'Openshift Quickstart for Redmine 3.0.1'
git remote add origin "ssh://$OPENSHIFT_APP_UUID@$OPENSHIFT_APP_DNS/~/git/$OPENSHIFT_GEAR_NAME.git/"
git push -u origin master
</pre>
