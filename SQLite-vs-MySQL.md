WeeWX supports both SQLite and MySQL, but, by default, uses SQLite. Here is a summary of the advantages and disadvantages of each:

## SQLite
### Pro:
- Simple access permissions. The database is held in a single file, so access permissions are set by
using the same permissions protocols used by any other file.
- Simple backup. Just copy the file.
- Very efficient when used on a single machine. See the article [Many Small Queries Are Efficient In SQLite](https://www.sqlite.org/np1queryprob.html).
- Comes with most modern operating systems, so it does not have to be installed.

### Con:
- Does not work well in a multi-user environment.
- Remote access across a network requires a remote file mount, which rarely work reliably.

## MySQL
### Pro:
- Works well in a networked, multi-user environment.

### Con:
- WeeWX tends to rely on many, small queries, which MySQL is ill-suited for.
- Complex administration. Many arcane commands must be studied and understood.
- Requires a separate install.
- Takes considerable system resources.

## Conclusion
SQLite is consistently underestimated. The SQLite database for my station has well over 1.2M records and 12 years of data and is over 250 MB in size. It continues to show great performance.

Unless you really require remote access and you are an experienced MySQL administrator, there is really no good reason to use MySQL.