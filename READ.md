DotNet application with helm charts folder "my-dotnet-app"

In the Cloud-Assignment folder:
1.) Create an image from Dockerfile by using cmd: docker build -t <image_name>:<tag> .
2.) Run the docekr image with -p flag for port. Default port is 80
3.) To run the image using helm. Mention the image name in values.yaml file.
4.) Run helm -n application my-dotnet-app
