Install azure storage python package 


```python
import os, uuid
from azure.storage.blob import BlobServiceClient, BlobClient, ContainerClient, __version__
try:
    print("Azure Blob Storage v" + __version__ + " - Python quickstart sample")
except Exception as ex:
    print('Exception:')
    print(ex)

# Azure Blob Storage v12.9.0 - Python quickstart sample
```

Get connection string for Azure blob storage from azure portal

```python 
connect_str = "DefaultEndpointsProtocol=https;AccountName=<>;AccountKey=<>;EndpointSuffix=core.windows.net"
#Alternatively you can create an env var for `!export AZURE_STORAGE_CONNECTION_STRING=`
```


Create container

``` python
# Create the BlobServiceClient object which will be used to create a container client
blob_service_client = BlobServiceClient.from_connection_string(connect_str)

# Create a unique name for the container
container_name = str(uuid.uuid4())

# Create the container
container_client = blob_service_client.create_container(container_name)
```

Create 

```python
# Create a local directory to hold blob data
local_path = "./data"
os.mkdir(local_path)

# Create a file in the local data directory to upload and download
local_file_name = str(uuid.uuid4()) + ".txt"
upload_file_path = os.path.join(local_path, local_file_name)

# Write text to the file
file = open(upload_file_path, 'w')
file.write("Hello, World!")
file.close()

# Create a blob client using the local file name as the name for the blob
blob_client = blob_service_client.get_blob_client(container=container_name, blob=local_file_name)

print("\nUploading to Azure Storage as blob:\n\t" + local_file_name)

# Upload the created file
with open(upload_file_path, "rb") as data:
    blob_client.upload_blob(data)
```


Listing Blobs

```python
print("\nListing blobs...")

# List the blobs in the container
blob_list = container_client.list_blobs()
for blob in blob_list:
    print("\t" + blob.name)
```


Download

```python 
download_file_path = os.path.join(local_path, str.replace(local_file_name ,'.txt', 'DOWNLOAD.txt'))
print("\nDownloading blob to \n\t" + download_file_path)

with open(download_file_path, "wb") as download_file:
    download_file.write(blob_client.download_blob().readall())
```

Delete

```python
# Clean up
print("\nPress the Enter key to begin clean up")
input()

print("Deleting blob container...")
container_client.delete_container()

print("Deleting the local source and downloaded files...")
os.remove(upload_file_path)
os.remove(download_file_path)
os.rmdir(local_path)

print("Done")
```
