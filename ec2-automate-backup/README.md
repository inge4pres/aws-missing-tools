# Introduction:
ec2-automate-backup was created to provide easy backup/snapshot functionality for EC2 EBS volumes. Common uses would include the following:
* run ec2-automate-backup with a list of volumes for which a snapshot is desired (example: `ec2-automate-backup.sh -v "vol-6d6a0527 vol-636a0112"`)
* run ec2-automate-backup with a list of volumes for which a snapshot is desired and allow the created snapshots to be deleted after 31 days (example: `ec2-automate-backup.sh -v "vol-6d6a0527 vol-636a0112" -k 31`)
* run ec2-automate-backup using cron to produce a daily backup (example: `"0 0 * * 0 ec2-user /home/ec2-user/ec2-automate-backup.sh -v "vol-6d6a0527 vol-636a0112" > /home/ec2-user/ec2-automate-backup_`date +"%Y%m%d"`.log"`)
* run ec2-automate-backup to snapshot all EBS volumes that contain the tag "Backup=true" (example: `"0 0 * * 0 ec2-user /home/ec2-user/ec2-automate-backup.sh -s tag -t "Backup=true" > /home/ec2-user/ec2-automate-backup_`date +"%Y%m%d"`.log"`)

# Directions For Use:
## Example of Use:
`ec2-automate-backup -v vol-6d6a0527`

the above example would provide a single backup of the EBS volumeid vol-6d6a0527. The snapshot would be created with the description "vol-6d6a0527_2012-09-07".
## Required Parameters:
ec2-automate-backup requires one of the following two parameters be provided:

`-v <volumeid>` - the "volumeid" parameter is required to select EBS volumes for snapshot if ec2-automate-backup is run using the "volumeid" selection method - the "volumeid" selection method is the default selection method.
    
`-t <tag>` - the "tag" parameter is required if the "method" of selecting EBS volumes for snapshot is by tag (-s tag). The format for tag is key=value (example: Backup=true) and the correct method for running ec2-automate-backup in this manner is ec2-automate-backup -s tag -t Backup=true.
## Optional Parameters:
`-r <region>` - the region that contains the EBS volumes for which you wish to have a snapshot created.

`-s <selection_method>` - the selection method by which EBS volumes will be selected. Currently supported selection methods are "volumeid" and "tag." The selection method "volumeid" identifies EBS volumes for which a snapshot should be taken by volume id whereas the selection method "tag" identifies EBS volumes for which a snapshot should be taken by a key=value format tag.

`-c <cron_primer_file>` - running with the -c option and a providing a file will cause ec2-automate-backup to source a file for environmental configuration - ideal for running ec2-automate-backup under cron. An example cron primer file is located in the "Resources" directory and is called cron-primer.sh.

`-n` - tag snapshots "Name" tag as well as description

`-k <purge_after_days>` - the period after which a snapshot can be purged. For example, running "ec2-automate-backup.sh -v "vol-6d6a0527 vol-636a0112" -k 31" would allow snapshots to be removed after 31 days. purge_after_days creates two tags for each volume that was backed up - a PurgeAllow tag which is set to PurgeAllow=true and a PurgeAfter tag which is set to the present day (in UTC) + the value provided by -k.

`-p` - the -p flag will purge (meaning delete) all snapshots that were created more than "purge after days" ago. ec2-automate-backup looks at two tags to determine which snapshots should be deleted - the PurgeAllow and PurgeAfter tags. The tags must be set as follows: PurgeAllow=true and PurgeAfter=YYYY-MM-DD where YYYY-MM-DD must be before the present date.
# Potential Uses and Methods of Use:
* To backup multiple EBS volumes use ec2-automate-backup as follows: `ec2-automate-backup -v "vol-6d6a0527 vol-636a0112"`
* To backup a selected group of EBS volumes on a daily schedule tag each volume you wish to backup with the tag "Backup=true" and run ec2-automate-backup using cron as follows: `0 0 * * 0 ec2-automate-backup -s tag -t "Backup=true"`
* To backup a selected group of EBS volumes on a daily and/or monthly schedule tag each volume you wish to backup with the tag "Backup-Daily=true" and/or "Backup-Monthly=true" and run ec2-automate-backup using cron as follows:
 - `0 0 * * 0 ec2-user /home/ec2-user/ec2-automate-backup.sh -s tag -t "Backup-Daily=true"`
 - `0 0 1 * * ec2-user /home/ec2-user/ec2-automate-backup.sh -s tag -t "Backup-Monthly=true"`
* To perform daily backup using cron and to load environment configuration with a "cron-primer" file:
 - `0 0 * * 0 ec2-user /home/ec2-user/ec2-automate-backup.sh -c /home/ec2-user/cron-primer.sh -s tag -t "Backup=True"`

`-u` - the -u flag will tag snapshots with additional data so that snapshots can be more easily located. Currently the two user tags created are Volume="ebs_volume" and Created="date." These can be easily modified in code.

# Additional Information:
the file "ec2ab - IAM User Required Permissions.json" contains the IAM permissions required to run ec2-automate-backup.sh in with the least permissions required as of 2012-11-21.

- Author: Colin Johnson / colin@cloudavail.com
- Date: 2013-02-17
- Version 0.9
- License Type: GNU GENERAL PUBLIC LICENSE, Version 3
