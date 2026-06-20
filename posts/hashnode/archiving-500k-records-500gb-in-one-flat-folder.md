---
title: "Archiving 500k records (500GB) in one flat folder"
slug: archiving-500k-records-500gb-in-one-flat-folder
publishedAt: 2019-07-12
brief: "I recently had to archive an old website which contain around 500k user uploaded files, totalling around 500GB. Due to the design of the application, the files were all in one folder with no subfolder"
tags: ["linux", "articles"]
---

I recently had to archive an old website which contain around 500k user uploaded files, totalling around 500GB. Due to the design of the application, the files were all in one folder with no subfolders.

I had assumed I would just take a backup, stick it on an external drive and provide it to the archiver.

Oh ye of little learning.

I don’t normally play around locally with such large filesets, so my first challenge was finding somewhere with enough space where I could download the files.

The files originated were from a Linux server and I work on a Mac, but for convenience of others, I planned to put the archive on a Windows-friendly formatted external drive.

Armed with a FAT32 formatted drive, I started copying and quickly discovered that FAT32 would handle folders containing this many files. Oops.

In MacOS, and on my NAS, I only have the Windows-friendly options of FAT32 and exFAT. exFAT seemed to solve my max-files issue, so I opted for that. As more of the data was copied to the exFAT formatted drive, the copying seemed to get slower and slower, to the point where virtually nothing was happening. I then learnt that apparently exFAT is [specifically designed for sold state drives](https://appuals.com/ntvs-vs-exfat/) and, using it with a disk drive, and with such large folders, exposed this painfully.

Having created a separate volume using ext4 (Linux friendly), I can confirm that copying to this filesystem was no issue.

In the end, I used [tar and split](https://www.tecmint.com/split-large-tar-into-multiple-files-of-certain-size/) to combine the files into a small number of large files (125 x 4GB). Copying these to an exFAT formatted disk drive was no problem.

In summary, moving large numbers of files around can be a painful process; plan accordingly. When designing apps allowing uploads, probably better to create subfolders where possible (by user, by date, etc.)

FAT32 doesn’t like large numbers of files in one folder. exFAT runs out of juice with disk drive. ext4 works fine in most cases, but is not friendly to Windows users. Tar and Split can be very helpful.