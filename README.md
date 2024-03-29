ARCHIVED
========

This project is no longer maintained.  If someone would like to adopt it I'll be happy to transfer it.

---

# S3BackupSimple

"What is the simplest thing that could possibly work"

## Quickstart

Simple backup script to run commands and send the output to Amazon S3
storage.

If you have a bucket named 'backups', you can just run

    s3backup -fliename some-backup-series.tar tar -C /some/path cp .

Creates /some-backup-series.tar.1 in AWS bucket "backups", using the
default boto profile.  As you run it each day it will rotate up to
COUNT versions, then overwrite.

It uses local disk to store the file, then uploads to S3.

## Backup naming

Right now, the backup name is a modulous of the appropriate frequency.
Thus, your first backup might not be ".1" -- use the date, not the
suffix.  Making this rotate logically (a la "logrotate") is likely an
upcoming feature.

## Usage:

    usage: s3backup [-h] [-profile PROFILE] [-bucket BUCKET] [-count COUNT]
                    [-frequency {minute,hour,day,week}] [-path PATH] -filename
                    FILENAME [-gzip] [-verbose]
                    ...
    
    positional arguments:
      command               command to run, with all arguments
    
    optional arguments:
      -h, --help            show this help message and exit
      -profile PROFILE      boto profile name
      -bucket BUCKET        bucket name. defaults to 'backups'
      -count COUNT          count of daily backups to keep. Default 3
      -frequency {minute,hour,day,week}
                            Frequency of rotation. Default 'day'.
      -path PATH            path in bucket. Defaults to root of bucket.
      -filename FILENAME    backup name
      -gzip                 gzip the command output
      -verbose              output verbose information

## AWS Permissions/Policies

## The user writing the backups needs

     {
         "Version": "2012-10-17",
         "Statement": [
             {
                 "Effect": "Allow",
                 "Action": [
                     "s3:List*",
                     "s3:PutObjectACL",
                     "s3:PutObject"
                 ],
                 "Resource": [
                     "arn:aws:s3:::<BUCKET>"
                 ]
             }
         ]
     }

## The S3 bucket needs

     {
     	"Version": "2012-10-17",
     	"Statement": [
     		{
     			"Effect": "Allow",
     			"Principal": {
     				"AWS": "<USERARN>
     			},
     			"Action": [
     				"s3:PutObject",
     				"s3:PutObjectAcl"
     			],
     			"Resource": "arn:aws:s3:::<BUCKET>/*"
     		}
     	]
     }

