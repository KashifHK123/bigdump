# bigdump
Staggered import of large and very large MySQL Dumps (like phpMyAdmin dumps) even through the web servers with hard runtime limit and those in safe mode. The script imports only a small part of the huge dump and restarts itself. The next session starts where the last was stopped.
