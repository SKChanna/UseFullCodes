# Image compress

```java

package com.image.fileupload.util;

import java.io.ByteArrayOutputStream;
import java.util.zip.Deflater;
import java.util.zip.Inflater;

public class ImageUtil {

    public static byte[] compressImage(byte[] data) {

        Deflater deflater = new Deflater();
        deflater.setLevel(Deflater.BEST_COMPRESSION);
        deflater.setInput(data);
        deflater.finish();

        ByteArrayOutputStream outputStream = new ByteArrayOutputStream(data.length);
        byte[] tmp = new byte[4*1024];
        while (!deflater.finished()) {
            int size = deflater.deflate(tmp);
            outputStream.write(tmp, 0, size);
        }
        try {
            outputStream.close();
        } catch (Exception e) {
        }
        return outputStream.toByteArray();
    }

    public static byte[] decompressImage(byte[] data) {
        Inflater inflater = new Inflater();
        inflater.setInput(data);
        ByteArrayOutputStream outputStream = new ByteArrayOutputStream(data.length);
        byte[] tmp = new byte[4*1024];
        try {
            while (!inflater.finished()) {
                int count = inflater.inflate(tmp);
                outputStream.write(tmp, 0, count);
            }
            outputStream.close();
        } catch (Exception exception) {
        }
        return outputStream.toByteArray();
    }
}
```

# Imagee From Url to Save Storage

```Java
        Url imageUrl = new Url("https://site.com/image.jpeg");  // Sample url, replace with yours

        String destinationFile = "sample.jpg";

        /*******************Multipart Upload Method*********************************
        **              To resources like minio or DB
        ***************************************************************************/
        
        /******** 
        * Step 1 
        * Create Buffered Image by Reading from Url using ImageIO library
        ********/
        BufferedImage image = ImageIO.read(imageUrl);

        /******** 
        * Step 2 
        * Create ByteArrayOutputStream object to handle Image data
        ********/
        ByteArrayOutputStream byteArrayOutputStream = new ByteArrayOutputStream();
 
        /******** 
        * Step 3 
        * Write as Image with Jpeg extension to byteArrayOutputStream created in previous step
        ********/
        ImageIO.write(image,"jpg",byteArrayOutputStream);

        /******** 
        * Step 4
        * Flush image created to byteArrayOutoputStream
        ********/
        byteArrayOutputStream.flush();

        /******** 
        * Step 5 
        * Create Random file name but unique by adding timestamp with extension
        ********/
        String fileName = RandomString.make() + new Date().getTime() + ".jpg";

        /******** 
        * Step 6
        * Now Create MultipartFile using MockMultipartFile by providing 
        * @param fileName name of the file
        * @param imageType like "image/jpg"
        * @param ByteArray from ByteArrayOutputStream
        ********/
        MultipartFile multipartFile = new MockMultipartFile(fileName,fileName,imageType,byteArrayOutputStream.toByteArray());
        byteArrayOutputStream.close() // Close once it is done saving
          
        /******** 
        * Step 7 
        * Now call Upload/Save method as you want
        ********/
        
        fileUpload(multipartFile);   // call your upload/save method here
        /***********file will be uploaded now*****************/
  
        /*****************File save Method*********************
        ***            To Some file like sample.jpg         ***
        ******************************************************/

        /******** 
        * Step 1 
        * Create Input Stream from Url to store fetched file as stream temporarily
        ********/
        InputStream inputStream = imageUrl.openStream();
        
        /******** 
        * Step 2 
        * Create Output Stream to write Imput Stream Data to a file locally
        ********/
        OutputStream outputStream = new FileOutputStream(destinationFile);
        
        /******** 
        * Step 3 
        * Create Helper Variables for handling the Write process
        ********/
        byte[] byteArray = new byte[2048];   // A byte array for checking the end of data stream
        int length;   // length for data stream

        /******** 
        * Step 4
        * Set While loop to write data from Input Stream to file using Output Stream until the end of stream is finished and exit
        ********/
        while ((length = inputStream.read(byteArray)) != -1) {
          outputStream.write(byteArray, 0, length);   // Will write data to file byte by byte of size 2048
        }

        /******** 
        * Step 5
        * Close both Output Stream and Input Stream Connections
        ********/
        inputStream.close();
        outputStream.close();
        /************ File is saved now***********************/
```

