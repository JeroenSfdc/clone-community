At time it can be useful to clone a Community (DXP). If you don't want to fool around with the existing community, just clone it on the same or another sandbox.
The Experience Bundle API can help with this. But there's a bit of work to it. Something which of course could be automated with a funky `sfdx cli` plug-in command. For now just the manual steps.

<details>
<summary>Create the new Community</summary>
First of all to create a new DXP Site, we either do it manually through Setup or instead use sfdx force:communicate:create. Beware the command runs in the background for a bit, so you'd have to wait a minute or two.

`sfdx force:community:create -n 'MyClonedDXP' -p 'expbundle-xxxx.csxxx.force.com' -t 'Customer Service' -d 'A Cloned community'`
</details>

<details>
<summary>Retrieve the community</summary>
Retrieve using MDAPI or pull the changes to your local IDE.
You should see the Experience Bundle folder structure. Which you'll overwrite.
</details>

<details>
<summary>Copy the experience bundle folders</summary>
Copy all the experience bundle folders from the Community you'd want to clone. Simple overwrite those folders in the new new Community's folder.

![image](https://user-images.githubusercontent.com/27854769/113352559-b3df2a80-933c-11eb-8b50-b51e216ee4d7.png)
</details>

<details>
<summary>Update the UUIDs in the `JSON` files</summary>
The Experience Bundle's folders files are knitted together using UUID. Because these are the ones you cloned, they need to be changed. The most simplistic approach would be to replace all UUIDs with a different value. Say we just change the last 6 characters to `ffffff` (or whatever sequence you think of, doesn't really matter). 
  
- `cd ./experiences/<your DXP site>`
- `egrep -ro '[0-9a-f]{8}-([0-9a-f]{4}-){3}[0-9a-f]{12}' . > ../uuids.csv`

Which will fetch UUIDs into a file which is saved in `../uuids.csv`
</details>

<details>
<summary>Create a series of `sed` commands to update the UUIDs</summary>
Using Excel or Google Sheet create a list of `sed` commands.
  
- **Column A** contains the filename including path (from the previous step)
- **Column B** contains the original UUID found (from the previous step)
- **Column C** can be a formula `=CONCAT(LEFT(B1;32);"ffff")`
- **Column D** can be a formula `=CONCAT("sed -i'' -e ";"'s/";B1;"/";C1;"/g'";" ";A1)`
</details>

<details>
<summary>Execute the sed commands</summary>  
Just copy the contents of your **Column D** and paste them into your terminal.
It can take a while to execute, depending on the size of your community. 
</details>

<details>
<summary>Delete the back-up files created by sed</summary>

`cd ./experiences/<your DXP site>`
`find . -name '*.json-e' -delete`
</details>

<details>
<summary>Deploy the Community</summary>
Deploy the Experience Bundle.
</details>

<details>
<summary>Publish the Community</summary>
sfdx force:community:publish -n '<your DXP site>'
</details>

Done!
