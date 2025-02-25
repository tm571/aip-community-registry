# Compute Module Geocoding Server

## Introduction
This is an example of integrating a server into a compute module. By attaching an OpenAPI specification in your Docker image labels, you can use an existing server without any special adapter code or conversion logic, and have your endpoints automatically parsed into ready-to-use Functions. 

We are running a [Nominatim](https://nominatim.org/) server, which is an open-source project for geocoding based on publicly available [OpenStreetMap](https://www.openstreetmap.org/about) data. This server is hosting the `/search` and `reverse` endpoints from Nominatim 3.7.2. `/search` allows you to enter a query string (eg. "Buckingham Palace") and get back information such as latitude and longitude, while `reverse` allows you to enter a latitude and longitude and get back information such as the street address. You can find more documentation on these endpoints [here](https://nominatim.org/release-docs/3.7/api/Overview/).

## Building Your Image

First, navigate to the `/code` directory. The `Dockerfile` contains all necessary instructions to build a Docker image which will run the Nominatim server. Note the `LABEL server.openapi` line, which is where we specify the endpoints our server is hosting so that we can seamlessly integrate it into Foundry.

To build the image, you first need data for your Nominatim server to use. OpenStreetMap data can be downloaded through [Geofabrik](https://download.geofabrik.de/). 

Next, create an Artifacts Repository in Foundry. Navigate to the `Publish` tab, and select `Docker` from the dropdown. Follow the provided command to build your Docker image, specifying the path to your data via the `OSM_DATA` build argument. For example:
```commandline
docker build --platform linux/amd64 -t example.palantirfoundry.com/nominatim-server:0.0.1 --build-arg OSM_DATA=./greater-london-latest.osm.pbf .
```

Next, use the provided command to publish your built image to your Artifacts Repository.

## Integrating in Compute Modules
Create a Compute Module in Foundry in the same project as your Artifacts Repository. Navigate to the `Configure` tab, then select `Add container` under the `Containers` section and choose your Artifacts Repository and published image. 

Next, navigate to the `Functions` tab and select `Detect from OpenAPI specification` under `Function detection mode`. Functions will be automatically parsed from the OpenAPI specification in the image labels, ready to use throughout Foundry - no adapters or manual conversion necessary. Click `View OpenAPI specification` to view the full server specification and more details about the inputs and outputs.

Select the `nominatimReverse` and `nominatimSearch` functions and import them, then click `Start` on your compute module to deploy your server. Once the compute module has started, you can test your Functions through the `Query` bar on the `Overview` page, as well as use them throughout Foundry, such as in Workshop or AIP Logic.


## Upload Package to Your Enrollment

The first step is uploading your package to the Foundry Marketplace:

1. Download the project's `.zip` file from this repository
2. Access your enrollment's marketplace at:
   ```
   {enrollment-url}/workspace/marketplace
   ```
3. In the marketplace interface, initiate the upload process:
   - Select or create a store in your preferred project folder
   - Click the "Upload to Store" button
   - Select your downloaded `.zip` file

![Marketplace Interface](./../_static/upload_product_banner.png)

## Install the Package

After upload, you'll need to install the package in your environment. For detailed instructions, see the [official Palantir documentation](https://www.palantir.com/docs/foundry/marketplace/install-product).

The installation process has four main stages:

1. **General Setup**
   - Configure package name
   - Select installation location

2. **Input Configuration**
   - Configure any required inputs. If no inputs are needed, proceed to next step
   - Check project documentation for specific input requirements

3. **Content Review**
   - Review resources to be installed such as Developer Console, the Ontology, and Functions

4. **Validation**
   - System checks for any configuration errors
   - Resolve any flagged issues
   - Initiate installation


## SDK Configuration (Optional)

Some packages include applications built with the Ontology SDK. These require additional setup:

1. Locate the SDK application code in the `app/` directory of the project repository

2. The following details will need to added to the source code for the application.  
   - Navigate to Developer Console: `{enrollment-url}/workspace/developer-console`
   - Find the installed application
   - Copy the following details:
     - CLIENT ID
     - Enrollment URL `{enrollment-url}.palantirfoundry.com`

3. Configure your development environment:
   - Add to `env.development` file under `app/`
   - (optional) Configure CORS in your control panel to allow `http://localhost:8080`

### Local Development
<p align="center">
<img width="650" src=./../_static/start%20developing.png>
</p>

**To run the application locally:**
1. Access the Developer Console's "Start Developing" section
2. Follow the "Add Ontology SDK" setup process)
3. In the `/app` directory, start the development server:
   ```sh
   npm run dev
   ```
   This will launch your application at `http://localhost:8080`