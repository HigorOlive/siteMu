---
layout: post
date: 2016-06-06
title: Moving assets from Dropbox to S3
---

Way back in the days when I was first building this Rails app, not knowing much about Amazon services, I decided to use Dropbox's Public folder to store and serve all the images (it was bad, I know). As time passes by, Public folder is no more, and I had to do the grownup thing and migrate files to a real CDN/storage service. I just did it, and it was surprisingly painless.

First I took the chance to upgrade [Paperclip](https://github.com/thoughtbot/paperclip), cleaned up the path constructor to fix some unideal paths, and changed the storage settings to S3.

Next I found [rclone](http://rclone.org/), rclone syncs files across cloud storages. I used a spare computer to run the sync job from Dropbox to the S3 bucket, it took around 14 hours.

During this time, I made an inventory of all the file name differences, and wrote a simple script to rename the files.

Once the sync was completed, I pushed the changes to heroku as a review app, sharing the production database. Connecting to the review app console, I did a dry run of the renaming script, which uncovered a bunch of unexpected file names... I made a bunch more changes and did a few more dry-runs, and eventually successfully renamed all the files. Using the review app, I confirmed all the links were indeed working.

Then I tried syncing again with rclone to see what files were uploaded since the last run. Rclone allowed me to do a dry-run to see what changes would be made, making I realized that given all the name changes rclone wanted to copy/remove all the changed files, so it was a no-go. Worst case scenario, I can still manaully copy all the files. So I logged into Dropbox and checked the recent file changes. Luckily it was quite manageable, so I copied and renamed them one by one.

Finally I set up Cloudfront and changed the app settings, and that was all!

All in all I was really surprised at how smooth the process was, so this is a note to self that things aren't always as tedious as I imagine them to be. 😂