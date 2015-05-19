btrfs-snappy
============

btrfs-snappy creates and maintains the history of snapshots of BtrFS filesystems by taking snapshots of a BtrFS mountpoint or subvolume ONLY when there are new or changed files BUT NOT deleted or renamed files.

Installation
------------
Place btrfs-snappy in /usr/sbin and make it executable, "chmod +x /usr/sbin/btrfs-snappy"

Features:
---------

* only takes snapshots when there are important data changes to your files (changed data in your files or the creation of non-empty new files, but not deleted or renamed files/folders -- i.e., data changes to a data block, not changes to an inode)
* can snapshot inside of the filesystem or rooted in a base directory
* can purge old snapshots when too many exist
* can keep multiple snapshot schedules for the same filesystem

btrfs-snappy is designed to be called from cron on a regular schedule.

Usage:
------

btrfs-snappy [-r] [-b basedir] mountpoint prefix count

* mountpoint is the filesystem to snapshot
* prefix is the prefix, which usually corresponds to a schedule (e.g. daily, weekly, monthly, etc.)
* count is the number of snapshots with the same prefix to keep
* -r makes the snapshot readonly (requires btrfs-tools v0.20)
* -b basedir places the snapshot in a like-named directory rooted in the basedir.  Without -b, snapshots are placed in a directory called .snapshots at the top of the mountpoint

mountpoint must be a mounted btrfs filesystem (i.e. appears in the output of
"mount -t btrfs") or an unmounted btrfs subvolume (which must exit 0 when
"btrfs su sh" is called on it).  Unmounted subvolumes require a recent
version of btrfs-progs; see "OS Support" below for specifics.

Cron Examples:
--------------

```cron
*/5   *   *   *   *   btrfs-snappy -r -b "/Snapshots/" "/My Documents/" hourly 30;
0     0   *   *   *   btrfs-snappy -r -b /Snapshots /home daily 7;
```

The above cronjob would

* Create a snapshot of "/My Documents" every 5 minutes, keeping 30 generations in "/Snapshots/My Documents"
* Create a snapshot of /home every day, keeping 7 generations in /Snapshots/home

OS Support
----------

Tested on

* Fedora 22
* Centos 7

License
-------

This program is distributed under the GNU General Public License

http://www.gnu.org/licenses/gpl.txt

Authors
-------

Originally by Birger Monsen <birger@birger.sh>

Readonly and basedir additions by James FitzGibbon <james@nadt.net>

VFS snapshot naming support by gitmopp (https://github.com/gitmopp)

Support for snapshotting unmounted btrfs subvolumes by Brian Kloppenborg (https://github.com/bkloppenborg)

Check for changes between current mountpoint or subvolume and last snapshot by artfulrobot (http://serverfault.com/users/96883/artfulrobot)

Take snaphots only when there are new or changed files, but not deleted or renamed files by David Carriere (<redwoodcare@hotmail.com>)
