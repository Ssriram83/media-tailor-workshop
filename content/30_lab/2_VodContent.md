+++
title = "(Optional) Insert Ads for VoD Content"
chapter = false
weight = 20
+++

This is an optional step, it's similar to live setup with below changes. 

Test your live stream URL (i.e. *${VOD_SOURCE})* in  [JW Player Stream Tester](https://developer.jwplayer.com/tools/stream-tester/). 

In our case, vod URL : https://ftc-vod-ingest-videos-002826676743-us-east-1.s3.amazonaws.com/converted/HLS.m3u8

Now Refer step 3 for making changes to your code again, update the below env variable and build and deploy the code:  

```yaml
VAST_URL : '{{YOUR_API_ENDPOINT}}'
```

Now test if the deployment has been successful by executing the below command in terminal

``` shell
curl YOUR_API_ENDPOINT/vod
```

This will return a response as below. We will be using this in our VOD ad insertion purposes. 

```xml
<vmap:VMAP xmlns:vmap="http://www.iab.net/videosuite/vmap" version="1.0">
 <vmap:AdBreak timeOffset="start" breakType="linear" breakId="preroll">
  <vmap:AdSource id="preroll-ad-1" allowMultipleAds="false" followRedirects="true">
   <vmap:AdTagURI templateType="vast3">
    <![CDATA[https://tbbnb0dpm9.execute-api.us-east-1.amazonaws.com/Prod/live/]]>
   </vmap:AdTagURI>
  </vmap:AdSource>
 </vmap:AdBreak>
 <vmap:AdBreak timeOffset="00:00:30.000" breakType="linear" breakId="midroll-1">
  <vmap:AdSource id="midroll-1-ad-1" allowMultipleAds="false" followRedirects="true">
   <vmap:AdTagURI templateType="vast3">
    <![CDATA[https://tbbnb0dpm9.execute-api.us-east-1.amazonaws.com/Prod/live/]]>
   </vmap:AdTagURI>
  </vmap:AdSource>
 </vmap:AdBreak>
 <vmap:AdBreak timeOffset="end" breakType="linear" breakId="postroll">
  <vmap:AdSource id="postroll-ad-1" allowMultipleAds="false" followRedirects="true">
   <vmap:AdTagURI templateType="vast3">
    <![CDATA[https://tbbnb0dpm9.execute-api.us-east-1.amazonaws.com/Prod/live/]]>
   </vmap:AdTagURI
  </vmap:AdSource>
 </vmap:AdBreak>
</vmap:VMAP>
```

Now we have all required components, so lets configure AEMT:

1. Follow the pre-requisites section. You can skip this step if already done. Now that the custom ADS is created, it's time to add AWS Elemental Media Tailor to the workflow.
2. Open the [Amazon AWS Elemental Media Tailor console](https://console.aws.amazon.com/mediatailor/). 
3. Click **Create Configuration**. 
4. Under **Configuration name** input "SummitVOD"
5. Under **Video content** source input  with out file name i.e. HLS.m3u8

https://ftc-vod-ingest-videos-002826676743-us-east-1.s3.amazonaws.com/converted/

6. Under *Ad Decision Server* input 

```shell
YOUR_API_ENDPOINT/vod?correlation=[session.id]&gender=[player_params.gender]&geo=[player_params.geo]
```

7. Click **Create configuration**
8. Navigate back to the AWS Elemental Media Tailor dashboard.
9. Click on the link for your AWS Elemental Media Tailor Configuration

Note: You should see your configuration displayed in the table on screen. Next to it you will see the *Playback endpoints* (it may take a few minutes to propagate)

10. Open configuration and copy the **HLS Playback Prefix**
11. To play the content, paste the appended playback URL and append “HLS.m3u8” into either the Safari browser or to the [JW Player Stream Tester](https://developer.jwplayer.com/tools/stream-tester/). Your link should look something like this:  https://<HLS_Playback_URL>/summit_vod/HLS.m3u8

Note: The first time the content is served, you may not see ads due to the “On-the-Fly” transcode video normalization. Once the ads have been transcoded and stored in cache, you will view the media file with the advertisements inserted at the start.

