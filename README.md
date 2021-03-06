# faas-img2ansi

[![Go Report Card](https://goreportcard.com/badge/github.com/johnmccabe/faas-img2ansi)](https://goreportcard.com/report/github.com/johnmccabe/faas-img2ansi) [![](https://images.microbadger.com/badges/image/johnmccabe/faas-img2ansi.svg)](https://microbadger.com/images/johnmccabe/faas-img2ansi "Get your own image badge on microbadger.com")

This repo contains an example [FaaS](https://github.com/alexellis/faas) function which uses the [johnmccabe/img2ansi](https://github.com/johnmccabe/img2ansi) Go library to convert an image to an ANSI string.

Currently the following image formats are supported:

- `png`
- `gif`
- `jpeg`

It demonstrates the use of a multi-stage build (available in Docker CE 17.06+) to build the Go binary seperately to the FaaS function image so as to keep the size as small as possible.

**NOTE**: Its recommended at this point to stick to small images as the resize capability hasn't been added yet.

![](images/logo.png)

## Deploying the Function

Make sure you have deployed a FaaS stack to your cluster using the instructions on the [FaaS repo](https://github.com/alexellis/faas).

### Use the CLI (`faas-cli`)

**Get the CLI**

The [faas-cli](https://github.com/alexellis/faas-cli/) can be installed via `brew install faas-cli` or `curl -sSL https://get.openfaas.com | sudo sh`.

Now deploy the function as follows:

```
# faas-cli -action deploy -image=johnmccabe/faas-img2ansi -name=img2ansi -fprocess="/usr/bin/faas-img2ansi"
200 OK
URL: http://localhost:8080/function/img2ansi
```

### Testing the Function
Now that the function is running in your FaaS environment you can test it from the command line by running:

```
$ curl -SL https://raw.githubusercontent.com/johnmccabe/faas-img2ansi/master/images/gopher.png > gopher.png

$ curl localhost:8080/function/img2ansi --data-binary @gopher.png
```

**NOTE**: as mentioned earlier, you should stick to small images for the moment (32x32 or so) as the function does not currently scale down large images.

## Other ways to deploy the function

### Using Curl
The function can be deployed using curl to hit the API endpoint directly:
```
# curl -s --fail localhost:8080/system/functions -d \
'{ 
   "service": "img2ansi",
   "image": "johnmccabe/faas-img2ansi",
   "envProcess": "/usr/bin/faas-img2ansi",
   "network": "func_functions"
}'
```

### Using the UI
You can also create the function via the UI at http://localhost:8080/ui/, clicking the `Create New Function` link and entering the following data:

![](images/create_new_function.png)
