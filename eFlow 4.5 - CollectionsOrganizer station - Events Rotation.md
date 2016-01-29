## eFlow 4.5 - CollectionsOrganizer station - Events Rotation ##

### Question / Description: ###
I am looking for a way to know if a page was rotated and which rotation was applied by a user in the CollectionsOrganizer station (acting as ManualId). I just need to know which event is triggered on this station when user press the Rotate Right or Rotate Left Button. I need this because in our application we export the images at the begin of the workflow and in the Export we only export the data, so I need to apply the same rotation applied by eFlow on the page. For the one that did not require ManualId, using the TRM file I know the rotation applied by FormId, but unfortunately CooOrganizer station do not update the TRM file like the old ManualId station did. instead of this, it rotates both for you, TIF and JPEG in the collection. It creates a DRD file, but with 0 always in Rotation, so not useful to my needs.

A workaround will be re-export the pages who passed CollOrganizer station, but I would prefer to avoid this.

So, if you know the event triggered by CollOrganizer when the user press Rotation Button, please let me know,


### Answer: ###
Untested, but...some hints.

- Add a reference to efCollectionOrganizer.exe (Yes, you read that right and yes references to .exe files are perfectly legal)
- Grab the "Station" object (think IStationAccess in Completion, seems that was loosely based on the same concept)

    var station = StationAccess.Station;

- You care about the UI library. Reference the CollectionOrganizer.UI assembly and get a reference to the UI object



    var OrganizerUI=station.CollectionOrganizerUI;


- Grab the commands you're interested in. Let's go with "rotate left" for now

//Careful: there's a organizerUI.CommandRotationLeftCollectionPages command as well
//Which one you care about and what these two do is left as an exercise to the reader 
var command= organizerUI.CommandRotationLeftPage;

That's just one ICommand. Here everything starts becoming foggy. I can guide you to this point, but now you must face the last mile alone.. ;-)

(I assume you can just register a CommandDone event handler or something, but...yeah. Try it and see)

The steps I have used were:

1. Add the reference to eFCollectionOrganizer.exe
2. Customize the event OnPostStationUIInitialize adding the event handlers:


        	#region OnPostStationUIInitialize
    	public void OnPostStationUIInitialize(ITIsClientSrviceModule csm)
    	{
    		try
    		{
    		...
    			var commandRotateLeft = 	IStationAccess.Station.CollectionOrganizerUI.CommandRotationLeftPage;
    		 	commandRotateLeft.CommandDone += new EventHandler(CommandRotateLeft_CommandDone);
    			var commandRotateRight = 	IStationAccess.Station.CollectionOrganizerUI.CommandRotationRightPage;
    		 	commandRotateRight.CommandDone += new EventHandler(CommandRotateRight_CommandDone);
    		...
    		}
    		catch(Exception ex)
    		{
    			MessageBox.Show(ex.ToString())	
    		}
    	}

		void CommandRotateRight_CommandDone(object sender, EventArgs e)
		{
			if(currentRotation<270)
			{
				currentRotation=currentRotation+90;
			}
			else
			{
				currentRotation=0;	
			}
			currentCollData.get_PageByIndex((short)currentPageIndex).set_NamedUserTags(Constants.UserTags.ROTATION_MANUALID, currentRotation.ToString());
		}

		void CommandRotateLeft_CommandDone(object sender, EventArgs e)
		{
			if(currentRotation>0)
			{
				currentRotation=currentRotation-90;
			}
			else
			{
				currentRotation=90;	
			}
			currentCollData.get_PageByIndex((short)currentPageIndex).set_NamedUserTags(Constants.UserTags.ROTATION_MANUALID, currentRotation.ToString());
		}


The currentPageByIndex I set in another event. If you need more info, send me an email.

With this, I have the ROTATION_MANUALID usertag, the rotation applied.
	
    
    
    