online_map
==========

Website displaying the online map
* [opennauticalchart.org](http://opennauticalchart.org)

## Preconfig* Login into your GitHub account & delete Forked Repo of online_map* Delete your project (pipelinedemo for example) in your OpenShift Instance (Online Pro Tier, CDK, ..)* Delete the PVC and service account for Jenkins

# Demo Script

## Create Jenkins Pipeline1. Create an OpenShift project, `pipelinedemo` for example2. Click __Add to Project__3. Click __Browse Catalog__4. Type `jenkins` in the __Search__ field5. Click Select on the __Jenkins__ image stream that appears
6. Make sure __Add to Project__ points to your project7. Accept all default values, scroll down and click __Create__

 7.1 Acknowledge the warning and click __Create Anyway__
 8. Click __Continue to Overview__9. Explain the quota warning (due to PVC of this container) and that we are still good to go10. Click __Don’t show me again__ to dismiss the warning

## Deploy Original Code1. Search in Google for "__open nautical chart site:github.com__"
2. Look for the following: `https://github.com/OpenNauticalChart` - usually the first result
3. Click the __online_map__ repo
4. Click the __Clone or Download__ button and copy the link presented ( leave the browser tab up)
5. Return to the OpenShift broswer/tab and complete the following:
  5.1 Click __Add to project__
    5.2 Click __Browse Catalog__
    5.3 Click on the __PHP__ Family
    5.4 Click __Select__ on PHP with version __7__ selected
    5.5 Use these entries for the following fields:
     5.5.1 __Name:__ `nauticalchart-original`
      5.5.2 __Git Repository URL:__ `<paste the link from step 4 above>`
     5.6 Click __advanced options__ at the bottom of the popup. Scroll to bottom of page, change:
   5.6.1 __Resource Limits, Memory Limit__ from `512` MiB to `256` MiB
   5.7 Click __Create__
  5.8 Click __Continue to Overview__
i. Explain S2I build process.
j. When Deployment starts, click on __POD__ circle graphic , explain __Details__ tab
k. Switch to __Logs__ tab
l. Switch to __Terminal__ tab
m. Go to LH Navigation Menu __Overview__
n. Look for __APPLICATION__, then Click on __https://nauticalchart-original-pipelinedemo...__ 
i. There is an error, the advanced capabilities navigation is missing. We need to fix!

## Create & Deploy Fork1. Return to open nautical chart GitHub site
 2. Click the __Fork__ button in the upper right
  2.1 If you are presented with a choice of multiple accounts, select your personal account
  2.2 The fork with take about 10-15 seconds, be patient!
3. Click the __Clone or Download__ button and copy the link presented in your forked repo
4. Return to the OpenShift brower/tab and complete the following::
  4.1 Click __Add to project__
    4.2 Click __Browse Catalog__
    4.3 Click on the __PHP__ Family
    4.4 Click Select on __PHP__ with version __7__ selected
    4.5 Use these entries for the following fields:
      4.5.1 __Name:__ `nauticalchart-forked`
        4.5.2 __Git Repository URL:__ `<paste the link from step 3>`
    
  4.6 Click __advanced options__ at the bottom of the popup. Scroll to bottom of page, change:
    4.6.1 __Resource Limits, Memory Limit__ from `512` MiB to `256` MiB
    4.7 Click __Create__
  4.8 Click __Continue to Overview__
  4.9 Wait for build to complete
  4.10 Show app - still broken, we just forked the same code.

5. We’re not going to make changes in master, we’re going to create a branch
 6. In GitHub , click the __Branch: master__ button
7. Type `fix` in the __Find or create a branch...__
text field, then click the new button (in blue) to create the fix branch
  7.1 You will automatically be switched to the `fix` branch
  8. Back in OpenShift, click LH Navigation Menu __Builds__ , then __Builds__ in the sub-menu
9. Click __nauticalchart-forked__ in the list of builds
10. Click __Actions__ (button in the upper right) and then __Edit__
11. Click the link under the Git Repository URL for __advanced options__
12. Change the __Git Reference__ from `master` to `fix`
13. Scroll down to the Triggers section and copy the __Generic Webhook__ URL
14. Click the __Save__ button at the bottom of the form
15. On the Build screen, click __Start Build__ to rebuild the app
16. Return to GitHub , click on the __Settings__ tab at the top of the repo
17. On the left side, click the __Webhooks__ link
18. Click the __Add webhook__ button - you may need to authenticate to GitHub with your password
19. Paste the Generic Webhook URL from Step 13 into the __Payload URL__ field
20. Change the __Content Type__ to `application/json`
21. Click the __Disable SSL__ button and acknowledge the warning
  21.1 Note - you would normally not do this and you can mention that if necessary
22. Click the __Add webhook__ button
  22.1 GitHub will send a test payload to OpenShift to confirm that the webhook works. This shouldtrigger a rolling rebuild of the nauticalchart-forked application. You should see the buildnumber increment

23. Click on/ __go to Overview__

##Create Canary Route1. Click on the __Applications__ button on the left menu bar then __Routes__ on the sub-menu2. Click on the __nauticalchart-original__ in the route table3. Click __Actions__ in the upper right, then __Edit__4. Check the box for Split Traffic5. Select “nauticalchart-forked” from the Service drop-down6. Set the Service Weights to 95% of the traffic to original and 5% to forked7. Click the Secure Route check box and keep all of the defaults8. Click Save at the bottom of the screen