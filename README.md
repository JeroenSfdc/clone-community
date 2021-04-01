Cloning a DXP Site, we can leverage the experienceBundle Metadata API. By retrieving an existing DXP site, we cannot simply rename and deploy it. There's a bit more to it.

First of all to create a new DXP Site, we either do it manually through Setup or instead use sfdx force:communicate:create. Beware the command runs in the background for a bit, so you'd have to wait a minute or two.

sfdx force:community:create -n 'MyClonedDXP' -p 'expbundle-abinbev-ei-crm.cs105.force.com' -t 'Customer Service' -d 'A Cloned community'

Next, copy the complete experience bundle to a folder matching your created DXP. Be sure to append the '1' behind the name of the folder. And make sure you update the name of the .site-metadata.xml file as well.

The example below shows the cloned community being MyABIB2BCommunityTEST1
image.png

Also edit the .site-metadata.xml following the information you used to create the community. Alternatively you can retrieve the newly created DXP site's Experience Bundle.

image.png

The next step is to change all of the UUID values inside the copied metadata. As those UUIDs are referring back to the original DXP from which is got copied.

This can be done in a couple of steps (and sure, there are more elegant methods possible)

1 / Find all UUIDs in all the files

cd ./experiences/<your DXP site>

Then

egrep -ro '[0-9a-f]{8}-([0-9a-f]{4}-){3}[0-9a-f]{12}' . > ../uuids.csv

Which will fetch all into a file which is saved in ../uuids.csv

The output will look similar to below

image.png

2/ Create a series of sed commands

In order to update all the files with a slightly modified UUID, we can create a series of sed commands. By just replacing the original UUID with a set of modified trailing characters. Say change the final 4 digits into ffff. If this is done across all files, they will remain consistent. The likeliness a pre-existing UUID ends in ffff taken for granted.

image.png

Assuming:

Column A contains the filename including path
Column B contains the original UUID found
Column C can be a formula =CONCAT(LEFT(B1;32);"ffff")
Column D can be a formula =CONCAT("sed -i'' -e ";"'s/";B1;"/";C1;"/g'";" ";A1)
3/ Execute sed commands

Copy column D and execute in your terminal. This can take a bit of time.

Once done you'll see that sed create back-up files. Something I was unable to workaround on OSX.

4/ Remove the back-up files

cd ./experiences/<your DXP site>

Then

find . -name '*.json-e' -delete

5/ Deploy the new DXP bundle

Following normals MDAPI deploy.

6/ Publish the DXP site

Finally, deploy through sfdx CLI.

$ sfdx force:community:publish -n '<your DXP site>'
