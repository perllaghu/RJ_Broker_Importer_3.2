# RJ_Broker_Importer_3.2

This is the SWORD importer to receive deposit packages from the 
Repository Junction Broker. 

It is applicable to EPrints 3.2.2 to 3.2.9
For EPrints 3.3, use https://github.com/edina/RJ_Broker_epm

## INSTALLATION

There are two options when it comes to inastalling these files: download the .zip file & unpack, or "clone" the
repository as a working GIT system.

The only difference is in how you get the files into your EPrints repository

### INSTALLATION VIA ZIP


Step 1: Download the .zip file & unpack somewhere safe

Step 2: copy the files into their appropriate places:

   cp -r cfg/* /<path_to_eprints_root>/archives/<archive_id>/cfg

### INSTALLATION VIA GIT-CLONING

Step 1: Greate a blank GIT repo at /path/to/eprints/archives/<ARCHIVEID>/

   git init
   
Step 2: Pull the files down into your file-tree

   git pull https://github.com/edina/RJ_Broker_Importer_3.2.git
   
### FOR BOTH   

Step 3: Restart your server.

You can check that the new configuration has been loaded by getting the SWORD
servicedocument & looking for the sword:acceptPackaging with a value of
"http://opendepot.org/broker/1.0"

For deposits to go direct into the live archive (rather than going into the 
review queue), you need to enable that facility within the local SWORD 
configuration.

Edit the file /<path_to_eprints_root>/archives/<archive_id>/cfg/cfg.2/sword.pl
and un-comment the section that starts 
	"archive" => {
			title => "Live Repository",


When arranging registration details with Repository Junction Broker, the 
"collection" part will be one of:
   /sword-app/deposit/inbox   (which goes into the users personal workspace)
   /sword-app/deposit/review  (which goes into the review queue for the repo)
   /sword-app/deposit/archive (which goes directly into the live repository)


Finally, we need to fix a flaw in the core EPrints code, so that SWORD deposits 
return the URI for the deposited item, not just the ID number.
The code is on the file ~/ePrints/perl_lib/EPrints/Sword/Utils.pm, and you need 
to edit just one line - find the line:

	$uid->appendChild( $session->make_text( $eprint->get_id ) );

And edit it to read:

	$uid->appendChild( $session->make_text( 
                                        $session->get_repository->get_conf( "base_url" )
                                      . '/id/eprint/'
                                      . $eprint->get_id 
                                        )

                            );

## TESTING

The directory "example export files" contains some sample records, along with instructions
for testing the raw deposit process.
