
We can have a centralized storage (FTP?) along side with IPFS.

Step1. Try to get it from local IPFS. If exists, use local. If not continue next step

Step2. Try the centralized storage to download actor code file. If exists, download it. If not, go to next step.

Step3. Try to get from remote IPFS using CID. 


Last step. As long as file is downloaded, upload to local IPFS is not exists before.

In order to make it consistency, use CID as the file name in the centralized storage service. Such as http://our_storage_service_url/cid/qmQWERASDFAXCV...

In order to get this CID, the best solution would be let the centralized file storage save to local IPFS first.

