# BigDump
![Dashboard](https://raw.githubusercontent.com/KashifHK123/bigdump/master/Dashboard.png "Dashboard")

![Import Progress](https://raw.githubusercontent.com/KashifHK123/bigdump/master/importing-progress.png "Import Progress")

![Modify these lines according to your username, password, database and sql file.](https://raw.githubusercontent.com/KashifHK123/bigdump/master/Usage-step-2.png "Modify these lines according to your username, password, database and sql file.")

Features
------------
- Staggered import of large and very large MySQL Dumps (like phpMyAdmin dumps) even through the web servers with hard runtime limit and those in safe mode. The script imports only a small part of the huge dump and restarts itself. The next session starts where the last was stopped.

- You want to restore the very large backup of your mySQL database into the new or the same mySQL database. You can’t access the server shell and you can’t import the dump using phpMyAdmin or any other scripts due to hard memory resp. runtime limit of the web server.

- BigDump does staggered import of large and very large MySQL Dumps (like phpMyAdmin dumps) even through the web servers with hard runtime limit and those in safe mode. The script executes only a small part of the huge dump and restarts itself. The next session starts where the last was stopped.

Requirements
------------
1. Bigdump script bigdump.php from the download
2. Dump file of your database created by phpMyAdmin or other tool, lets call it dump.sql. You can also use GZip compressed dump files, lets call it dump.gz.
3. Access account for your mySQL database
4. Access account for some web server with PHP and mySQLi installed. This web server must be able to connect to the mySQL database. This ability is probably present if your web server and the mySQL server are from the same ISP.
5. Some good text editor like Sublime/Notepad++ to edit the configuration file.
6. Some FTP client to upload the files to the web server.
7. Common knowledge about files, PHP, mySQL databases, phpMyAdmin, FTP and HTTP



Usage
------------

1. ownload and unzip bigdump.zip on your PC.
2. Open bigdump.php in a text editor, adjust the database configuration and dump file encoding. 
3. Drop the old tables on the target database if your dump doesn’t contain “DROP TABLE” (use phpMyAdmin).
4. Create the working directory (e.g. dump) on your web server
5. Upload bigdump.php and the dump files (*.sql or *.gz) via FTP to the working directory (take care of TEXT mode upload for bigdump.php and dump.sql but BINARY mode for dump.gz if uploading from MS Windows).
6. Run the bigdump.php from your web browser via URL like http://www.yourdomain.com/dump/bigdump.php.
7. Now you can select the file to be imported from the listing of your working directory. Click “Start import” to start.
8. BigDump will start every next import session automatically if JavaScript is enabled in your browser.
9. Relax and wait for the script to finish. Do NOT close the browser window!
10. IMPORTANT: Remove bigdump.php and your dump files from your web server.

Usage Notes
- Note 1: BigDump will fail processing large tables containing extended inserts. An extended insert contains all table entries within one SQL query. BigDump isn’t able to split such SQL queries. In most cases BigDump will stop if some query includes to many lines. But if PHP complains that allowed memory size exhausted or MySQL server has gone away your dump probably also contains extended inserts. Please turn off extended inserts when exporting database from phpMyAdmin.

- Note 2: If you want to upload the dump files via web browser give the scripts writing permissions on the working directory (e.g. make chmod 777 on a Linux based system). You can upload the dump files from the browser up to the size limit set by the current PHP configuration of the web server. Alternatively you can upload any files via FTP. Some web servers disallow script execution in the directory with writing permissions for security reasons. If you changed the permissions on the working directory and you are getting a server error when running the script restore the permissions to their normal state (chmod 755) for directories.

- Note 3: If Timeout errors still occur you may need to adjust the $linespersession setting in bigdump.php.

- Note 4: If mySQL server overrun occurs due to max_requests restriction you can use $delaypersession setting to let the script sleep some milliseconds or more before starting next session. This setting will only work if the JavaScript is activated. Importing dump file on such servers may be very time consuming.

- Note 5: BigDump is currently not able to restore a single dump file with multiple databases inside (switched by the USE statement). BigDump is also not able to restore a single specific database from the dump file containing multiple databases.

- Note 6: If you experience problems with non-latin characters while using BigDump you have to adjust the $db_connection_char_set configuration variable in bigdump.php to match the encoding of your dump file.

- Note 7: GZip support is only available with PHP 4.3.0 and later. Using a huge GZip compressed dump file can cause the script to exceed the PHP memory/runtime limit since the dump file has to be unpacked from the beginning every time the session starts. If this happens use the uncompressed dump. It’s your only chance.

- Note 8: It’s not a very good idea, but if you can also import from CSV file into one mySQL table using Bigdump. You have to specify the table name in $csv_insert_table. Please also check other CSV settings in the Bigdump configuration.


FAQs
---

**Q: Bigdump stops on extended inserts. Help!
A:** Bigdump isn’t able to handle dump files with extended inserts. It’s a limitation on how Bigdump splits the files and probably won’t be resolved in the future. Please turn off extended inserts when exporting database from phpMyAdmin.

----

**Q: How can I export a database from phpMyAdmin without extended inserts?
A:** In the older versions of phpMyAdmin look for extended inserts switch in the export settings and set it to off. In the newer phpMyAdmin versions select Include column names in every INSERT statement for Syntax to use in the Data dump options of the export dialog.

----

**Q: The import process is very slow. What happens?
A:** The speed of the dump import process is mostly depending on the mySQL server performance. The same dump file can take few minutes or few hours to import depending on mySQL performance of the particular system. Bigdump can’t make the import process faster or slower. It just gives your dump file query by query to the mySQL server.

----

**Q: I get an error: “MySQL: Bad syntax near DEFAULT CHARACTER SET…”. Why?
A:** You are probably trying to restore a dump file from a newer mySQL database version into an older mySQL database. Sorry, this is obviously not a very good idea and this can’t work. You have to recreate the dump file in compatibility mode or remove all incompatible stuff by hand.

----

**Q: I get an error: “MySQL: Table ‘some_tbl_name’ already exists”. Why?
A:** Your dump file doesn’t contain DROP queries. Use phpMyAdmin to drop all the tables on the target database which must be restored before you start the import.

----

**Q: I get an error: “MySQL: Variable ‘xyz’ can’t be set to the value of ‘NULL'”. Why?
A:** Your dump file probably contains a statement like this: `SET character_set_client = @saved_cs_client;` Bigdump is not able to restore settings saved at the beginning of the import process. However you can skip this error message by adding a comment setting `$comment[]='SET character_set_client = @saved_cs_client;';` to the BigDump configuration.

----

**Q: I get an error: “MySQL: Duplicate entry ‘X’ for key Y”. Why?
A:** There is something broken in your dump file as it tries to create duplicated entries where a unique key is defined by CREATE TABLE statement. In some cases it can still work if you ignore all these errors. In order to do so you have to replace all INSERT INTO by INSERT IGNORE INTO in your dumpfile using a text editor.

---

**Q: I get PHP errors “Value to large” when trying to import a file larger than 2 GB. Why?
A:** Some PHP versions are broken and can’t handle files largen than 2 GB. Ask your hoster to apply this [PHP patch](https://news-web.php.net/php.internals/32767 "PHP patch") for solution.

---

**Q: I get an error: “Fatal error: Allowed memory size of xxx bytes exhausted” or “MySQL server has gone away”. Why?
A:** Your dump file probably contains extended inserts. An extended insert contains all table entries within one SQL query. BigDump isn’t able to split such SQL queries. Please turn off extended inserts when exporting database from phpMyAdmin.

----

**Q: Why does BigDump fail putting strange SQL errors if I run it on MS Windows based server?
A:** This is IMHO a bug in the PHP4 and PHP5 that is crashing BigDump on Win32 if using a dump file with DOS encoded line breaks (contact me for details). As workaround create your dump with only the UNIX line breaks or convert it into UNIX format using some text editor. Then you will be able to run BigDump also on Win32.

----

**Q: I get an error: “PHP version 4.1.0 is required for BigDump to proceed. You have PHP 4.3.x installed. Sorry!”. Why?
A:** This is strange but it happens. Something is wrong with you PHP installation. As workaround you can remove from bigdump.php five code lines following on the comment` // Check PHP version`. This will skip the version check.

----

**Q: Can I use dump files created by other software than phpMyAdmin?
A:** You can use any text dump file at your own risk. Although it’s very important for BigDump to find a semicolon followed by a line break at the end of each query. It won’t work else since it has no time to parse the queries to find their proper ends. Further BigDump behaviour can be unpredictable if your dump file is using double quotes to surround strings. BigDump won’t work too if your dump file contains any proprietary [comment lines](https://dev.mysql.com/doc/refman/8.0/en/comments.html "comment lines") (like in some dumps created by other tools). Although you can use the $comment setting in bigdump.php to drop non-standard comment lines by their first characters. Apart from that BigDump doesn’t filter the dump file in any way.

----

**Q: Do you plan to add a feature for creating/exporting large mySQL dumps?
A:** Actually no. Creating large dump files usually works well even with phpMyAdmin or other web tools. If your database is too large for that you should think about other hosting solution which allows shell access.

----

**Q: Why dont you provide any facility to input the database configuration from the browser form?
A:** Input the database configuration from the form would be very insecure since the settings must be saved in the cookies or returned to your browser everytime the session ends.

----

**Credit goes to [Fully Factory GmbH](https://www.ozerov.de/bigdump/ "Fully Factory GmbH")**

----

######  THIS SCRIPT IS PROVIDED AS IS, WITHOUT ANY WARRANTY OR GUARANTEE OF ANY KIND.

This program is free software; you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation; either version 2 of the License, or (at your option) any later version. If you change this script or add any features please tell me. I will consider adding these features for everybody.
