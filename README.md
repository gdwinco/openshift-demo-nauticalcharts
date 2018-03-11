online_map
==========

Website displaying the online map
* [opennauticalchart.org](http://opennauticalchart.org)

Special thanks to Michael Epley (mepley@redhat.com) for sharing this demo script

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

## Create Canary Route1. Click on the __Applications__ button on the left menu bar then __Routes__ on the sub-menu2. Click on the __nauticalchart-original__ in the route table3. Click __Actions__ in the upper right, then __Edit__4. Check the box for __Split Traffic__5. Select __nauticalchart-forked__ from the __Service__ drop-down6. Set the __Service Weights__ to 95% of the traffic to __nauticalchart-original__ and 5% to __nauticalchart-forked__7. Click the __Secure route__ check box and keep all of the defaults8. Click __Save__ at the bottom of the screen

## Add Readiness Check
1.  Use the LH Navigation, click __Application__, then __Deployments__ in the sub-menu
2. Click __nauticalchart-forked__ in the build list3. Click the __Actions__ button in the upper right, then select __Edit Health Checks__4. Click __Add Readiness Probe__5. Change the __Type__ to __Container Command__6. In the __Command__ field, type `sh` (don't use quotes) then click __Add__ at the end of the line.7. In the __Add argument__ field below the `sh` command, enter `-c` (dont' use quotes) then click __Add__ at the end of the line.8. In the __Add argument__ field, enter `curl localhost:8080 | grep -i weather` (don't use quotes)then click __Add__ at the end of the line.8. Click __Save__ at the bottom of the page. Note that this will trigger a rebuild.10. Use the LH menue to return to the __Overview__ screen and expand the __nauticalchart-forked__ app11. Notice that the deployment seems to be stuck. Click __Events__ tab12. Notice that the readiness probe we just created has failed and the pod is “Unhealthy.” Let’s fix that.13. Go back to GitHub - you’re probably still on the webhook screen, so click the online_map link at the topnext to your account name - this will return you to the repo - verify the __fix__ branch (change to it if needed)14. Click on the __index.php__ file in the content list, this will show you the file contents.15. Click the __Edit__ button in the upper right (looks like a pencil)16. Near the bottom of the file, change the `<?` to `<?php`

 16.1 Add a comment to your change (Fixed missing php, or something like that)
 18. Click __Commit Changes__19. Click the __online_map__ link next to the Branch:fix (NOT the one at the top of the page)20. Now select the __weather_index.php__ file from the content list.21. Click the __Edit__ button in the upper right (looks like a pencil)22. Near the bottom of the file, change the `<?` to `<?php` (without the quotes)
 22.1 Add a comment to your change (Fixed missing php, or something like that)
 23. Click __Commit Changes__
24. Now return to OpenShift and click LH Navigation Menu for __Overview__ - you should be in time to see the rebuild to the fixed version of the app.
  24.1 Explain that this was actually 2 rebuilds - one for each commit in Git - show build numbers
 25. Wait for the build to complete then in the __ROUTES Expernal Traffic__ Select the __Route__ __nauticalchart-forked__(NOT original) and wait for the app to load.
26. You should now have a menu at the top left. Click the View button and enable Weather. This will takeseveral seconds to load, but you should now see a weather overlay.

## Now, let’s create a pipeline!1. Use the LH Navigation to the return to the OpenShift __Overview__2. Go to and copy the contents`https://raw.githubusercontent.com/gdwinco/openshift-demo-nauticalcharts/master/resources/buildpipeline-nauticalchart-repair-original.json`3. Click the __Add to Project__ in the upper RHS of the screen4. Click __Import YAML/JSON__5. Paste in the contents of the file6. Click __Create__7. In OpenShift, click __Builds__ on the left menu pane, the __Pipelines__ in the sub-menu8. Click __nauticalchart-repair-pipeline__9. Click __Actions__, __Edit__10. Go to and copy the contents`https://raw.githubusercontent.com/gdwinco/openshift-demo-nauticalcharts/master/resources/jenkinsfile-nauticalchart-repair-original.json`11. Paste into the edit box, replacing All existing contents12. Click __Save__13. Click __Start Pipeline__14. Return to OpenShift __Overview__ and expand __nauticalchart-forked__15. Scroll to the bottom and notice the pipeline build working.16. Once the build enters the Test phase, it pauses for user input, click __input required__
 16.1 Note that this will open the Jenkins page, you may have to accept permissions here.
 17. Once you are presented with the Paused for Input page, select __Proceed__18. Return to the OpenShift __Overview__ and watch the remainder of the pipeline build.19. Notice the following:
 19.1 The build scales to 2 pods 19.2 The build changes the traffic split to 100% forked 19.3 The original build removes the nauticalchart-original app. This is commented out to allow the demo to be rerun without going through all of the steps again. See "Reset Pipeline" below.20. Huzzah!

## (Optional) Resetting the Pipeline

1. The `resources` of the github repo`https://raw.githubusercontent.com/gdwinco/openshift-demo-nauticalcharts/master/resources/` contains two aditional files:

 1.1 `build-pipeline-nauticalchart-reset-original.json`
 1.2 `jenkinsfile-nauticalchart-forked-reset.json`
 
2. You can optionally use these files, and the steps in "Now, let's create a pipeline" to reset the nauticalchart-forked app to create another pipeline that resets the state of __nauticalchart-forked__ app to its state before the __nauticalchart-repair-pipeline__ was run

This allows demonstrating the build pipeline again
 