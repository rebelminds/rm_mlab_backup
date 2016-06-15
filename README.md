# mLab S3 Backup

This is a node project that makes backing up your mLab databases to S3.
<br>The config file specifies which database to back-up and includes cron "time" field to explicitly backup at a set time.

## Installation
<ul>
<li> Clone or download this repo. </li>
<li> To configure the backup, you need to pass the binary a JSON configuration file </li>
<li> Open <code>config.json</code> in your favourite code editor </li>
</ul>


## Configuration

The file should have the following format:

    {
      "sesTransport": {
        "to": "YOUR_EMAIL",
        "from": "SES_TRANSPORT_SERVER_EMAIL",
        "key": "SES_TRANSPORT_SERVER_KEY",
        "secret": "SES_TRANSPORT_SERVER_SECRET",
        "region": "SES_TRANSPORT_SERVER_REGION"
      },
      "mongodb": {
        "host": "SERVER NAME",
        "port": PORT_NUMBER,
        "username": "USERNAME",
        "password": "PASSWORD",
        "db": "DATABASE_TO_BACKUP"
      },
      "s3": {
        "key": "YOUR_S3_KEY",
        "secret": "YOUR_S3_SECRET",
        "bucket": "SS_BUCKET_TO_UPLOAD_TO",
        "destination": "/",
        "encrypt": true,
        "region": "S3_REGION_TO_USE"
      },
      "cron": {
        "time": "02:00",
      }
    }

A breakdown on how to setup each of these fields follows.

### Config.json / SES (Simple Email Service) Transport Fields
Nodemailer will email you if the s3 server is unreachable or there is a problem with extracting database data.
The SES Transport fields are used to configure nodemailer. 
However, emailing errors is entirely optional and leaving these fields blank will not effect the backup.

If you work for Rebel Minds, bother Max Bye for these details.

Otherwise, a configured Amazon SES is recommended and setup instructions can be found <a href="https://aws.amazon.com/ses/" target="_blank">here</a>. 

### Config.json / MongoDB Fields
The mLab database you want to backup.

<ul>
<li> Log into mLab </li>
<li> Navigate to a database that needs backing up</li>
<li> Locate mongoDB URI
<li> Fill in fields (use example below to help)</li>
</ul>
<img src="https://s3-eu-west-1.amazonaws.com/rm-mlab-backup/mLab+Details.png">
In the above instance, the MongoDB Fields would be considered so:

      "mongodb": {
        "host": "ds011321.mlab.co",
        "port": 11321,
        "username": "USERNAME USED TO SETUP DATABASE",
        "password": "PASSWORD SET WHEN SETTING UP DATABASE",
        "db": "max-test-keystone"
      }

### Config.json / S3 Fields
The S3 server details the database is being backed up to.
If you work for Rebel Minds, you want to send mlab data to the bucket <code>rm-mlab-backup</code>. 
Otherwise, if you haven't created an s3 bucket <a href="https://aws.amazon.com/s3/?sc_channel=PS&sc_campaign=acquisition_UK&sc_publisher=google&sc_medium=s3_b&sc_content=s3_e&sc_detail=s3%20amazon&sc_category=s3&sc_segment=62042444929&sc_matchtype=e&sc_country=UK&s_kwcid=AL!4422!3!62042444929!e!!g!!s3%20amazon&ef_id=VwyxeQAAAW1MGi8h:20160615140249:s">click here to get started</a>.



### Crontabs
By default, it is set to run every night at 2am.
You may optionally substitute the cron "time" field with an explicit "crontab"
of the standard format `0 0 * * *`.

      "cron": {
        "crontab": "0 0 * * *"
      }

*Note*: The version of cron that we run supports a sixth digit (which is in seconds) if
you need it.

For instance, 

      "cron": {
        "crontab": "00 30 11 * * 1-5"
      }

Runs every weekday (Monday through Friday) at 11:30:00 AM.

### Timezones

The optional "timezone" allows you to specify timezone-relative time regardless
of local timezone on the host machine.

      "cron": {
        "time": "00:00",
        "timezone": "America/New_York"
      }

You must first `npm install time` to use "timezone" specification.

## Running

Navigate to the the project root.
To start a long-running process with scheduled cron job:

    npm start

To execute a backup immediately and exit:

    npm run backupNow
    
And to test / debug:

    npm run debug
    
<a href="http://stackoverflow.com/questions/4018154/node-js-as-a-background-service" target="_blank">See here</a> for running this program in the background 

## To Load From Backup
<ul>
<li>Download database backup file from s3 to localhost</li>
<li>Unzip <code>.gz</code></li>
<li>Run <code>mongorestore</code> utility.

    mongorestore -h <backup directory> -d max-test-keystone -u <user> -p <password> <input db directory>

Where <code>input db directory</code> will the unzipped backup on the localhost.

## Special Thanks
Many thanks to <a href="https://github.com/theycallmeswift" target="_blank">TheyCallMeSwift</a> for <a href="https://github.com/theycallmeswift/node-mongodb-s3-backup/blob/master/bin/mongodb_s3_backup.js" target="_blank">mongodb_s3_backup.js</a>.