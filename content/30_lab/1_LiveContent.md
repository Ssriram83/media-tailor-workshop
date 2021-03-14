+++
title = "Insert Ads on a Live Content"
chapter = false
weight = 1
+++

### Step 1 - Test your Live Feed

For this lab, we are providing you a live video source.
https://d1wil155oo1tnj.cloudfront.net/out/v1/1237241bc90c412fab48edbb7e3747b9/index.m3u8

Test if this live feed works in [JW Player Stream Tester](https://developer.jwplayer.com/tools/stream-tester/)

When you click on **Test Stream** you should be able to see the live stream rendering properly as given in the below screenshot. 

![JW Player](/images/jw_player.PNG)

### Step 2 - Configure AWS Elemental Media Tailor

1. Open the Amazon AWS Elemental Media Tailor console at https://console.aws.amazon.com/mediatailor/. 
2. Click on  **Create Configuration**. 
3. Under **Configuration name** input `IndiaSummitLive`
4. Under **Video content** source input add your ${LIVE_SOURCE} URL without manifest name. In our case, the url is going to be `https://d1wil155oo1tnj.cloudfront.net/out/v1/1237241bc90c412fab48edbb7e3747b9/`

5. Under **Ad Decision Server** input, provide the API_ENDPOINT that we previously created. 

    ``` shell
    {YOUR_API_ENDPOINT}/live?correlation=[session.id]&gender=[player_params.gender]&geo=[player_params.geo]

    ```

This is how the screen might look like. 

![AEMT COnfig](/images/AEMT_Live.png)


6. Open **Personalization details** tab and under **Live pre-roll ad decision server** Input same :

```shell
 {YOUR_API_ENDPOINT}/live?correlation=[session.id]&gender=[player_params.gender]&geo=[player_params.geo]
```

![Personalization](/images/AEMS_Personalize.png)

7. Click **Create configuration** 
8. Navigate back to the AWS Elemental Media Tailor dashboard.
9. Open your AWS Elemental Media Tailor Configuration

Note: You should see your configuration displayed in the table on screen. Next to it you will see the **Playback endpoints** (it may take a few minutes to propagate)

10. Copy the **HLS Playback Prefix**
11. To play the content, paste the appended playback URL and append **index.m3u8** into either the Safari browser or to the [JW Player Stream Tester](https://developer.jwplayer.com/tools/stream-tester/). Your link should look something like this:  [https://<HLS_Playback_URL>index.m3u8](https://7e7ccbfb4c05400696da27a06cbbe888.mediatailor.us-east-1.amazonaws.com/v1/master/1ff1c90ae6fbc558a1f7f1c4631ee0ff55fcb49b/summit_live/index.m3u8)

Note: The first time the content is served, you may not see ads due to the “On-the-Fly” transcode video normalization.  Once the ads have been transcoded and stored in cache, you will view the media file with the advertisements inserted at the start.

