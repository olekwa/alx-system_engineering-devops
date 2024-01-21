**Postmortem Report: 504 Error Incident**

**Summary:**
On October 21st, 2022, at midnight, server access went down, causing a 504 error for anyone attempting to access the website. The server is based on a LAMP stack.

**Timeline:**
- **00:00 PST:** 500 error observed when accessing the website.
- **00:05 PST:** Verified Apache and MySQL status.
- **00:10 PST:** Website not loading properly; server and database checked, both working.
- **00:12 PST:** Apache server restarted, returning a status of 200 and OK during a curl attempt.
- **00:18 PST:** Reviewed error logs to identify the source of the error.
- **00:25 PST:** Checked /var/log; Apache server found to be prematurely shut down; PHP error logs not found.
- **00:30 PST:** Discovered all error logging in php.ini settings was turned off; enabled error logging.
- **00:32 PST:** Restarted Apache server, checked php error logs.
- **00:36 PST:** PHP error logs showed a mistyped file name causing premature closure of Apache.
- **00:38 PST:** Fixed file name, restarted Apache.
- **00:40 PST:** Server running normally, website loading properly.

**Root Cause and Resolution:**
The issue stemmed from an incorrect file name referenced in the wp-settings.php file. When attempting to curl the server, a 500 error was returned. No php error log was being created, leading to the discovery that error logging for php was turned off. Once enabled, the php error logs revealed a misspelled file extension in wp-settings.php. Fixing this resolved the error, and puppet code deployment corrected the file extension on all servers.

**Corrective and Preventive Measures:**
- Enable error logging on all servers and sites to quickly identify and address errors.
- Test servers and sites locally before deploying in a multi-server setup to catch and correct errors before going live, minimizing downtime.
