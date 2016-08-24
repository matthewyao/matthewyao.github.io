---
layout:     post
title:      Easy File Upload Using DropzoneJS and Spring
subtitle:   DropzoneJS is an open source library that provides drag and drop file uploads with image previews.
catalog:    true
tags:       [Spring, Upload, DropzoneJS, Java, 后端, ]
---



## Easy File Upload Using DropzoneJS nd Spring

>**[DropzoneJS](http://www.dropzonejs.com/)** is an open source library that provides drag’n’drop file uploads with image previews.

Saving user files in a web application is pretty much a necessity in many cases be it *images, videos, or documents.* This post will go over how to easily implement both the back and frontend components to facilitate the storage of files to a database. Additionally, we will be using **DropzoneJS** to prettify and make the front end uploading process more smooth. First we will lay down the backend framework to facilitate the persisting of user files to the database. We will start by going over the backend implementation by creating a basic REST server, using **Java Spring**, with endpoints to both accept and send files. This will then be followed up with a frontend implementation using html form and DropzoneJS as the library to upload files.

You can follow along by downloading the complete source found on [GitHub](https://github.com/enyo/dropzone/tree/gh-pages).

**It looks like this**

![introduction of dropzone.js](http://oc26wuqdw.bkt.clouddn.com/DropzoneJS_Introduction.png)

## Backend Setup

### Upload Files

We will start with setting up our REST server to accept file uploads. First we will create the base application:

```
@Controller
@RequestMapping("/file")
public class FileController {
...
@RequestMapping(value = "file_upload.do", method = RequestMethod.POST)
@ResponseBody
public ResultResponse<Object> uploadFile(MultipartHttpServletRequest request,@RequestParam String orderCode,@RequestParam int fileType,@RequestParam String assignedAe) {
    ResultResponse resultResponse = new ResultResponse();
    boolean success;
    try {
        Iterator<String> itr = request.getFileNames();
        while (itr.hasNext()) {
            String uploadedFile = itr.next();
            MultipartFile file = request.getFile(uploadedFile);
            success = fileService.saveFile(file,orderCode,fileType,assignedAe);
            resultResponse.setIsok(success);
        }
        resultResponse.setMessage("文件上传成功！");
    }catch (IOException e) {
        resultResponse.setIsok(false);
        resultResponse.setMessage("文件上传失败！");
        logger.error("文件上传失败:"+e);
        e.printStackTrace();
    }
    return resultResponse;
}
...
}
```
Here wu use **MultipartHttpServletRequest** to receive the upload files.In order to use **MultipartHttpServletRequest**,we should first config **org.springframework.web.multipart.commons.CommonsMultipartResolver** in spring like below

```
<bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
    <!-- set the max upload size100MB -->
    <property name="maxUploadSize">
        <value>104857600</value>
    </property>
    <property name="maxInMemorySize">
        <value>4096</value>
    </property>
</bean>
```

In this way we can use a post http request like `/file/file_upload.do` to upload files to server.
And we add a saveFile() function to save upload files,when we use **MultipartHttpServletRequest** we should use **MultipartFile** to get upload files and save them in a loop.

### Save files

We use **Java OutputStream** to save file to local storage.

```
public boolean saveFile(MultipartFile file, String orderCode, int fileType, String assignedAe) throws IOException {
    inputStream = file.getInputStream();
    File path = new File(dirPrefix);
    //判断上传文件的保存目录是否存在
    if (!path.exists()) {
        path.mkdirs();
    }
    //上传文件路径
    String originalFilename = file.getOriginalFilename();
    String filePath = dirPrefix + originalFilename;
    logger.info("--------------------上传文件路径: " + filePath);
    if ((new File(filePath)).exists()) {
        inputStream.close();
        return false;
    }
    fileOutput(inputStream, filePath);
    FileBaseInfo fileInfo = new FileBaseInfo();
    fileInfo.setFileName(originalFilename);
    fileInfo.setFileUrl(filePath);
    fileInfo.setAssignAeId(new Random().nextInt(5)+1);
    fileInfo.setUploadUserId(new Random().nextInt(5)+1);
    fileInfo.setOrderCode(orderCode);
    fileInfo.setFileType(fileType);
    fileDao.saveFileBaseInfo(fileInfo);
    return true;
}
```

### Download files

Download files use a Http RESTful GET url as `/file/file_download.do` to download file,and in order to escape Chinese garbled we use *ISO-8859-1* to recoding filename.

```
@RequestMapping(value = "file_download.do",method = RequestMethod.GET)
public ResponseEntity<byte[]> downloadFile(@RequestParam String filePath){
    File file = new File(filePath);
    ResponseEntity<byte[]> response = null;
    HttpHeaders headers = new HttpHeaders();
    headers.setContentType(MediaType.APPLICATION_OCTET_STREAM);
    try {
        String fileName=new String(file.getName().getBytes("UTF-8"),"ISO-8859-1");
        headers.setContentDispositionFormData("attachment", fileName);
        response = new ResponseEntity<byte[]>(FileUtils.readFileToByteArray(file),headers, HttpStatus.CREATED);
    } catch (IOException e) {
        e.printStackTrace();
    }
    return response;
}
```

## Front Setup

In front we use dropzone.js,you can see more at [DropzoneJS](http://www.dropzonejs.com/).First you should add a div with `id="dropzone"`,and then add a form action to file upload url, and add `enctype="multipart/form-data"` to enable multipart file uploaded.

```
<script type="text/javascript" src="${ctx}/static/js/dropzone.js"></script>
<link rel="stylesheet" type="text/css" href="${ctx}/static/css/dropzone.css">
...
<div id="dropzone">
    <form action="/file/file_upload.do" class="dropzone needsclick dz-clickable" id="demo-upload" enctype="multipart/form-data" method="post">

        <div class="dz-message needsclick">
            拖拽文件到这里或点击上传
        </div>

    </form>
</div>
```

At this point,you can drap or click form and choose file(s) then dropzone will auto upload files use **POST HTTP** request to `/file/file_upload.do`and save files to local storage.