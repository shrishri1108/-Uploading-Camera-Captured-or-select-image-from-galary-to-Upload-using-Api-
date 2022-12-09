# Uploading-Camera-Captured-or-select-image-from-galary-to-Upload-using-Api-

##@ Prerequistics


 1> In manifest file 
    a>>  
          <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
              <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
    
    b>> In <application >  tag         
         <provider
            android:name="androidx.core.content.FileProvider"
            android:authorities="${applicationId}.provider"
            android:exported="false"
            android:grantUriPermissions="true"
            tools:replace="android:authorities">
           <meta-data
                android:name="android.support.FILE_PROVIDER_PATHS"
                android:resource="@xml/file_paths"
                tools:replace="android:resource" />
          </provider>

      c>> In res -> xml  . Create  a file_paths.xml file  and paste following code into it . 
              <?xml version="1.0" encoding="utf-8"?>
         <paths>
                <external-path name="my_images" path="Android/data/<write_your_package_here>" />
                   <external-path
                    name="external_files"
                    path="." />

                <external-path name="external_files" path="."/>
 
                <external-path name="external" path="." />

               <cache-path name="cache" path="." />

               <external-cache-path name="external_cache" path="." />

               <files-path name="files" path="." />

          </paths>

  2>   In   Project-level  Build.gradle file -->  Inside allprojects { }
          
          repositories {
            google()
            jcenter()
            maven { url "https://jitpack.io" }
            flatDir {
                dirs 'libs'
             }
           }
 
   3>  In   Module-level Build.gradle file   --> add following dependency into it . 
           
           implementation 'com.github.dhaval2404:imagepicker:2.1'
          
          
Steps ===>> 



1>      In  onClick select btn's called  funtion  ()
           image_type_gallery = 1;
           ImagePicker.with( ReportDetailsActivity.this)
                        .crop()	    			//Crop image(Optional), Check Customization for more option
                        .compress(1024)			//Final image size will be less than 1 MB(Optional)
                        .maxResultSize(1080, 1080)	//Final image resolution will be less than 1080 x 1080(Optional)
                        .start();
                        }
                    });
                                     
2>>  Outside onCreate  Method  Override  onActivityResult() as following                    
           if(image_type_gallery ==1 ) {
                if(resultCode== RESULT_OK) {
                    File imageUriTOFile = new File(data.getData().toString());
                    Toast.makeText(this, " "+imageUriTOFile.getAbsolutePath(), Toast.LENGTH_SHORT).show();
                        etFile.setText(imageUriTOFile.getName().toString());
                    AddImage(new GetFileFromUriUsingBufferReader().getImageFile(this,data.getData()));
                }
                else if( resultCode == ImagePicker.RESULT_ERROR){
                    Toast.makeText(this,  ""+ ImagePicker.getError(data), Toast.LENGTH_SHORT).show();
                }
                else {
                    Toast.makeText(this, " Action Cancelled .", Toast.LENGTH_SHORT).show();
                }
  
              }
    

3>> Create a GetFileFromUriUsingBufferReader  Kotlin class  File  and write  following code into that 
          public class GetFileFromUriUsingBufferReader{
               fun getImageFile(mContext: Activity?, documentUri: Uri): File {
                   val inputStream = mContext?.contentResolver?.openInputStream(documentUri)
                   var file =  File("")
                   inputStream.use { input ->
                       file =
                           File(mContext?.cacheDir, System.currentTimeMillis().toString()+".jpg")
                       FileOutputStream(file).use { output ->
                           val buffer =
                               ByteArray(1 * 1024) // or other buffer size
                           var read: Int = -1
                           while (input?.read(buffer).also {
                                   if (it != null) {
                                       read = it
                                   }
                               } != -1) {
                               output.write(buffer, 0, read)
                         }
                        output.flush()
                       }
                     }
                     return file
              }

4>> outside  OnActivityResult() create a void AddImage()  as following ---    
          private void AddImage(File file2) {
              //  Call your  corresponding Api  using Retrofit   .   Following example  is goods   --->   
              
              RestClient.getInst().upload_a_image(file2).enqueue(new HttpCallback<Upload_astrologer_imageBean>() {
                  @Override
                  public void onSuccess(Call<Upload_astrologer_imageBean> call, Response<Upload_astrologer_imageBean> response) {
                  
                      if (response.body().getStatus()) {
                          Log.e("Data1myImgs1", "abc " + response.body().getFile_img());
                          imageFile = response.body().getFile_img();
                          dialogLoader.hideProgressDialog();
                          
                          
                      } else {
                          Log.e("Data11", "abc " + file2.getAbsolutePath());
                          // dialogLoader.hideProgressDialog();
                      }
     
                  }
   
                  @Override
                  public void onError(Call<Upload_astrologer_imageBean> call, Throwable t) {
                      Log.e("Data11", "abc " + t.getMessage());
                      dialogLoader.hideProgressDialog();
     
                  }
               });
               }
       
