+++
title = "Install the Pre-requisites"
chapter = false
weight = 20
+++

### Step 1 - Clone the Repo

The below repository contains all the pre-requisite assets that is used in this workshop. 

```
cd ~/environment
git clone https://github.com/Ssriram83/serverless_ad_decision_service
```

### Step 2 - Create Ad Inventory

In the folder `ads_video_assets`, we have included sample videos that will will act as your ad inventory which will be inserted in live and VOD streams. These Ad Inventory files must first be paced in a S3 Bucket so that Media Tailor can access it.

```
# Navigate to Ads Video Assets folder. 
cd ~/environment/serverless_ad_decision_service/ads_video_assets

# Generate a Unique ID and use that for creating Bucket Name
export S3_BUCKET_NAME=$(uuidgen)

#create an s3 bucket
aws s3 mb s3://$S3_BUCKET_NAME

#upload files to s3 and make them public. 
aws s3 sync . s3://$S3_BUCKET_NAME --acl public-read

#verify if the files are present 
aws s3 ls s3://$S3_BUCKET_NAME
```

### Step 3 - Create a Dummy Server-less Ad Decision Service

In order to insert Ads to your stream we need a component which will take decision on which ad should be inserted. i.e. an Ad Decision Service or ADS. AWS Elemental Media Tailor (*AEMT*) will get he advertisements from ADS and stitch it to your video streams. AEMT support almost all ADS who adhere to specifications from IAB (Interactive Advertising Bureau)

 Once you setup your ADS, you will get a ad tag which can be configured on AEMT. So if you have a ${AD_TAG}, then you can use same. Since we are assuming we don’t have one, will create a Dummy ADS which will give us a URL which can be used as our tag.

By using the Navigation Menu, find the folder "serverless_ad_decision_service"  and open file template.yml

![Edit Template](/images/edit_template.png)

Replace the value `YOUR_BUCKET_NAME` with the name of the S3 Bucket we created now. 
You can find the name of the S3 Bucket by executing the below command

```
echo $S3_BUCKET_NAME
```
This is how your template.yaml should look like after the changes

![template_yaml_after_changes](/images/template_yaml_after_changes.PNG)


Now execute the below command to deploy this application. 

```
sam build &&  sam deploy --resolve-s3
```

You will be prompted for a confirmation to deploy. Provide `Y` so that you proceed with the deployment.

This will deploy an API Gateway and Lambda that acts as a simple ADS. There are two endpoints that this template creates: */live* endpoint, which returns a VAST XML response. and */vod* endpoint which returns a vmap xml response.

If all goes well, you will see an endpoint as given below. 

![API URL](/images/api_url.PNG)

Copy endpoint URL from terminal (highlighted below as value) and keep it handy for next steps.

Now test if the deployment has been successful by executing the below command in terminal

```
curl YOUR_API_ENDPOINT/live
```

```xml
<VAST xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="vast.xsd" version="3.0">
  <Ad id="1999" sequence="99">
    <InLine>
      <AdSystem version="1.0">SABS Ad Server</AdSystem>
      <AdTitle>SABS</AdTitle>
      <Description/>
      <Survey/>
      <Error>
        <![CDATA[http://error.err]]>
      </Error>
      <Impression id="500">https://qkiipps372.execute-api.us-east-1.amazonaws.com/prod/sabs-ads</Impression>
      <Impression id="sample-server">https://qkiipps372.execute-api.us-east-1.amazonaws.com/prod/sabs-ads</Impression>
      <Creatives>
        <Creative id="199200">
          <Linear>
            <Duration>00:20:00</Duration>
            <TrackingEvents>
              <Tracking event="creativeView">https://qkiipps372.execute-api.us-east-1.amazonaws.com/prod/sabs-ads</Tracking>
            </TrackingEvents>
            <VideoClicks>
              <ClickThrough id="">https://qkiipps372.execute-api.us-east-1.amazonaws.com/prod/sabs-ads</ClickThrough>
            </VideoClicks>
            <MediaFiles>
              <MediaFile id="GDFP" delivery="progressive" width="640" height="360" type="video/mp4" bitrate="118" scalable="true" maintainAspectRatio="true">https://YOUR_S3_REPO/billpay.mp4</MediaFile>
            </MediaFiles>
          </Linear>
        </Creative>
        <Creative>
          <CompanionAds>
            <Companion width="300" height="250">
              <StaticResource creativeType="image/jpeg">http://companionad.com/image.jpg</StaticResource>
              <TrackingEvents>
                <Tracking event="creativeView">http://companionad.com/creativeView</Tracking>
              </TrackingEvents>
            </Companion>
          </CompanionAds>
        </Creative>
      </Creatives>
    </InLine>
  </Ad>
</VAST>
```