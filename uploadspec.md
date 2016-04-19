This specifies that C storage api surface for Stream/File upload.  Note that while it has IoTHub as a prefix for names, this would certainly changes for an actual implementation.

typedef struct IoTHub_Data_Uploader_Handle_Tag* IoTHub_Data_Uploader_Handle;

//
// The pumb has two ways to denote that io is completing and that any data sent shold be commited.
//
//    It may return a status of IoTHub_DataUploader_Complete.  That means that the pumb should not be called again and any data in the buffer argument should be written to storage and
//    all data previously written should be commited.
//
//    Additionally (or alternatively) the bufferLength could simply be set to 0.  That means that the pump should not be called again and all data should be commited.
//
typedef IoTHubIoTHub_DataUploader_Pump_Status (*IoTHub_DataUploader_Pump)(IoTHub_Data_Uploader_Handle uploader,void* pumbConetext, unsigned char** buffer, size_t* bufferLength);
typedef void* (*IoTHub_DataUploader_Termination)(IoTHub_Data_Uploader_Handle uploader, void* terminationContext, IotHub_DataUploader_Termination_Status status)

IoTHub_DataUploader_Handle IotHub_CreateDataUploader(
                               IoTHub_DataUploader_Storage_Specifier storageSpecifier,
                               IoTHub_DataUploader_Pump pump,
                               void* pumbContext,
                               IoTHub_DataUploader_Termination termination,
                               void* terminationContext
                               )
						
//
// This will destroy the passed in uploader.  Any active uploading will be terminated.  The termination function will invoked with a status of destroyed.  If there is NO active upload in progress
// the termination function will NOT be invoked.
// Any data NOT already committed WILL remain uncommited.
//
void IoTHub_Destroy_DataUploader(
         IoTHub_DataUploader_Handle uploader
		 )
						
//
// Given a previously create uploader handle, this function will start uploading to the destination.  It will create a new blob.  If there was already a blob with
// the given name that blob will be overwritten.
//						
void IoTHubIoTHub_DataUploader_NewUpload(
          IoTHub_DataUploader_Handle uploader
		  )
		  
//
// Given a previously create uploader handle, this function will start uploading to the destination.  Note that if the blob already exists, the newly pumped data will be appended
// to the end of all previouse sent committed or uncommited data.
//
// Appending could be used to implement a restart upload capability. If the application first calls IoTHubIoTHub_DataUploader_Existing_Size, it could use the returned size to seek to
// the appropriate point in the data source.
//						
void IoTHubIoTHub_DataUploader_AppendUpload(
          IoTHub_DataUploader_Handle uploader
		  )
		  

//
// The existingSize argument denotes both committed and uncommited data written to the destination.
//
// If some error (like network connection loss) occured, that will be indicated by the returned status.
// It is possible but certainly not guaranteed that repeating this call will be successful.
//
IoTHub_DataUploader_Status IoTHubIoTHub_DataUploader_Existing_Size(
                               IoTHub_DataUploader_Handle uploader,
                               size_t* existingSize
                               )
