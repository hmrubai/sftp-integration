# SFTP File System Configuration
It is a document on the implementation of Laravel SFTP configuration on fileSystem.

## Contributor

- **[Hosne Mobarak Rubai](https://github.com/hmrubai/)**


## Official Documentation

 - **[SFTP Laravel (Laravel 10+)](https://laravel.com/docs/11.x/filesystem#sftp-driver-configuration)**

### STEP 01: Install package
Before using the SFTP driver, you will need to install the Flysystem SFTP package via the Composer package manager:

```
    composer require league/flysystem-sftp-v3 "^3.0"
```

### STEP 02: Update config/filesystems.php
Laravel's Flysystem integrations work great with SFTP; however, a sample configuration is not included with the framework's default (config/filesystems.php) configuration file. If you need to configure an SFTP filesystem, you may use the configuration example below:

```
    ...
    'sftp' => [
        'driver' => 'sftp',
        'host' => env('SFTP_HOST'),
        'username' => env('SFTP_USERNAME'),
        'password' => env('SFTP_PASSWORD'),
        'root' => env('SFTP_ROOT'),
        'permPublic' => 0777,
        'directoryPerm' => 0777,
        'visibility' => 'public'
    ],
```

### Update .env file according to configuration

```
    FILESYSTEM_DISK=sftp

    SFTP_HOST="x.x.x.x"
    SFTP_PORT=22
    SFTP_USERNAME="Username"
    SFTP_PASSWORD="Password"
    SFTP_ROOT="root/path" 
```

### STEP 04: Add method to your project: 

```
    // Add Interface on top of the file
    use Illuminate\Support\Facades\Storage

    // Upload the file
    protected function ftpFileUpload($fullRequest, $fileName, $destination)
    {
        $file = null;
        $file_url = null;
        if ($fullRequest->hasFile($fileName)) {
            $file_temp = $fullRequest->file($fileName);
            $destinations = 'uploads/' . $destination;
            $file_url = Storage::put($destinations, $file_temp);
        }
        return $file_url;
    }

    // Upload and Replace file
    protected function ftpFileUploadAndUpdate($fullRequest, $fileName, $destination, $oldFile = null)
    {
        $file_url = null;
        if ($fullRequest->hasFile($fileName)) {

            if ($oldFile) {
                $old_image_path = public_path('uploads/'.$oldFile);
                if (file_exists($old_image_path)) {
                    Storage::delete($old_image_path);
                }
            }

            $file_temp = $fullRequest->file($fileName);
            $destinations = 'uploads/' . $destination;
            $file_url = Storage::put($destinations, $file_temp);
        }
        return $file_url;
    }

    // Delete File 

    protected function deleteFtpFile($file)
    {
        $file_path = public_path('uploads/'.$file);
        if (file_exists($file_path)) {
            Storage::delete($file_path);
        }

        return true;
    }
```

-- That's it!