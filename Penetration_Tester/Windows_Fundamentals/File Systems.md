# FAT32

- Works on most devices (USBs, SD cards, Windows, Mac, Linux)
- Max file size: 4GB
- No encryption or compression

---
# exFAT

- Modern replacement for FAT32 on flash storage 
- Supports larger files than 4GB 
- Cross-platform

---

# NTFS (default since Windows NT 3.1)

- Journaling file system with metadata support
- Supports large partitions
- Granular file/folder permissions
- Security: ACL-based access control
- Cons: limited support on older devices

The NTFS file system has many basic and advanced permissions. Some of the key permission types are:

|   Permission Type    |                                                                                                                                                                                        Description                                                                                                                                                                                         |
| :------------------: | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: |
|     Full Control     |                                                                                                                                                               Allows reading, writing, changing, deleting of files/folders.                                                                                                                                                                |
|        Modify        |                                                                                                                                                                  Allows reading, writing, and deleting of files/folders.                                                                                                                                                                   |
| List Folder Contents |                                                                                                                                  Allows for viewing and listing folders and subfolders as well as executing files. Folders only inherit this permission.                                                                                                                                   |
|   Read and Execute   |                                                                                                                                 Allows for viewing and listing files and subfolders as well as executing files. Files and folders inherit this permission.                                                                                                                                 |
|        Write         |                                                                                                                                                          Allows for adding files to folders and subfolders and writing to a file.                                                                                                                                                          |
|         Read         |                                                                                                                                                  Allows for viewing and listing of folders and subfolders and viewing a file's contents.                                                                                                                                                   |
|   Traverse Folder    | This allows or denies the ability to move through folders to reach other files or folders. For example, a user may not have permission to list the directory contents or view files in the documents or web apps directory in this example c:\users\bsmith\documents\webapps\backups\backup_02042020.zip but with Traverse Folder permissions applied, they can access the backup archive. |

---

# Share permissions

|Permission|Description|
|---|---|
|`Full Control`|Users are permitted to perform all actions given by Change and Read permissions as well as change permissions for NTFS files and subfolders|
|`Change`|Users are permitted to read, edit, delete and add files and subfolders|
|`Read`|Users are allowed to view file & subfolder contents|

# NTFS Basic permissions

|Permission|Description|
|---|---|
|`Full Control`|Users are permitted to add, edit, move, delete files & folders as well as change NTFS permissions that apply to all allowed folders|
|`Modify`|Users are permitted or denied permissions to view and modify files and folders. This includes adding or deleting files|
|`Read & Execute`|Users are permitted or denied permissions to read the contents of files and execute programs|
|`List folder contents`|Users are permitted or denied permissions to view a listing of files and subfolders|
|`Read`|Users are permitted or denied permissions to read the contents of files|
|`Write`|Users are permitted or denied permissions to write changes to a file and add new files to a folder|
|`Special Permissions`|A variety of advanced permissions options|

# NTFS Special permissions

|Permission|Description|
|---|---|
|`Full control`|Users are permitted or denied permissions to add, edit, move, delete files & folders as well as change NTFS permissions that apply to all permitted folders|
|`Traverse folder / execute file`|Users are permitted or denied permissions to access a subfolder within a directory structure even if the user is denied access to contents at the parent folder level. Users may also be permitted or denied permissions to execute programs|
|`List folder/read data`|Users are permitted or denied permissions to view files and folders contained in the parent folder. Users can also be permitted to open and view files|
|`Read attributes`|Users are permitted or denied permissions to view basic attributes of a file or folder. Examples of basic attributes: system, archive, read-only, and hidden|
|`Read extended attributes`|Users are permitted or denied permissions to view extended attributes of a file or folder. Attributes differ depending on the program|
|`Create files/write data`|Users are permitted or denied permissions to create files within a folder and make changes to a file|
|`Create folders/append data`|Users are permitted or denied permissions to create subfolders within a folder. Data can be added to files but pre-existing content cannot be overwritten|
|`Write attributes`|Users are permitted or denied to change file attributes. This permission does not grant access to creating files or folders|
|`Write extended attributes`|Users are permitted or denied permissions to change extended attributes on a file or folder. Attributes differ depending on the program|
|`Delete subfolders and files`|Users are permitted or denied permissions to delete subfolders and files. Parent folders will not be deleted|
|`Delete`|Users are permitted or denied permissions to delete parent folders, subfolders and files.|
|`Read permissions`|Users are permitted or denied permissions to read permissions of a folder|
|`Change permissions`|Users are permitted or denied permissions to change permissions of a file or folder|
|`Take ownership`|Users are permitted or denied permission to take ownership of a file or folder. The owner of a file has full permissions to change any permissions|

---

