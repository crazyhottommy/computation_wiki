###Potential Ingestion Errors
This document records commone errors that have been encountered during the ingestion runs for wes.  We will try to update this document as new common errors arise.


Error Type 1: Unauthorized user for upload
Possible error messages:
```bash
   {email address} not authorized to upload wes_analysis data to S1400I.  Please contact a CIDC administrator if you believe this is a mistake.
```
Error reason: Exactly what it says -- you need permissions from the software team.
Solution:
Contact Jen(jennifer@ds.dfci.harvard.edu) and she will follow-up with the software team.  Or, if Jen is not available, reach out directly to Joyce(jhong@ds.dfci.harvard.edu).


Error Type 2: Lost host connection
Possible Error messages:
```bash
   sudo: unable to resolve host wes-auto-cczrus6ml: Resource temporarily unavailable
```
```bash
   gcloud crashed (ConnectionError): HTTPSConnectionPool(host='oauth2.googleapis.com', port=443): Max retries exceeded with url: /token (Caused by NewConnectionError('<urllib3.connection.HTTPSConnection object at 0x7f2d6b3ff470>: Failed to establish a new connection: [Errno -3] Temporary failure in name resolution',))
```
Error reason: Automator redirects /tmp to /mnt/ssd/tmp so that the samples do not run out of /tmp disk space.  BUT if you don't reset /tmp to the original BEFORE stopping the instance, then when you start it up again, the internet connection gets mucked up. That is why users can't connect to host .
Solution:
The solution resets the original temp and restarting resets the internet settings/connectionsrun the following commands to reset /tmp & restart the instance.
```bash
  cd /
  sudo unlink tmp
  sudo mv tmp.bak tmp
```
After resetting tmp , stop the instance and start it again.

Error Type 3: Upload failures
Possible Error messages:
```bash
   Detected mismatch of upload_placeholder='0bbb416e-0c90-4e10-ae45-a343ceb3c125' and upload_placeholder='2a0adbb9-2352-4b6b-97ff-79eb495297c9' in run_id='CCZRFQ92Y.01'
```
Error reason: This error often means that the sample has already been ingested to the portal.
Solution: Go to the production portal and see if the sample is already present.  If it is not there, contact Jen and she will follow-up with the software team.

Error Type 4: Server Internal Error
Possible error messages:
```bash
   API server error: The server encountered an internal error and was unable to complete your request.
```
Error reason: There can be multiple reasons for internal errors, but most recently, this error occurred due to the ingestion xlsx file being corrupt.
Solution:
Obtain a new xlsx sheet from Jen.


Error Type 5: Missing files
Possible error messages:
```bash
   Could not locate the following required files:
   /mnt/ssd/wes/analysis/CCZR0PSBY.01_error.yaml
   /mnt/ssd/wes/analysis/rna/CCZR0PSBY.01/CCZR0PSBY.01.haplotyper.rna.vcf.gz, /mnt/ssd/wes/analysis/rna/CCZR0PSBY.01/CCZR0PSBY.01_tnscope.filter.neoantigen.vep.rna.vcf
```
Error Reason: The ingestion script is not handling optional files properly.
Solution:
This was solved by adding the analysis folder path to the ingestion spreadsheet.
If you get this error, then add this in the ingestion spreadsheet.  The latest spreadsheets sent by Jen should have “/mnt/ssd/wes/” added to this field.
