#Getting started with RoboComp #1

##A simple component

In this tutorial you will be learning to create a simple component using robocompDSL Editor to simulate a bot moving in a square.

Let's get started, First we start the DSLEditor by executing

	cd /robocomp/tools/roboCompDSL/DSLEditor
	./DSLEditor

Next we create a new folder wherever convinient andc create a new `.CDSL` file.

	 1. Right click > Create new folder > name the folder
	 2. Right click on the newly created folder > Create new > Other > RoboCOmpDSLs > CDSL File > 		    Next > Name the CDSL file

For example, The folder is named as `firstcomp` and the cdsl file is also named `firstcomp.cdsl`.

Now open the `firstcomp.cdsl`, The `interfaces` will be found in a tab below the workspace. Search for `DifferentialRobot` and add it with the green plus mark on the right side. Add the `requires` line in the code and your `firstcomp.cdsl` should look like this.	

	import "/robocomp/interfaces/IDSLs/DifferentialRobot.idsl";
	Component firstcomp{
		Communications{

			requires DifferentialRobot;

		};
		language Cpp;
	};
	

In the course of this tutorial you will be learning about other interfaces for now, Save the file and now we generate the rest of the code.

	1. In the Menu bar, click on RoboComp > Generate code

Navigate to the `firstcomp` folder and see the files that are generated and you can close the DSLEditor. Now let us build the component.

	cd path/to/firstcomp
	cmake .
	make

Your component is now built and ready to be programmed to obtain the desired result. Note that this is the general procedure that is to be followed every time when using DSLEditor for component creation.

Now let us program the bot to move in a square.

`DifferentialRobot` interface offers certain function that you can use to make your program simpler. One of the function is

	differentialrobot_proxy->setSpeedBase(x, y);

This function is used to set the speed of the bot `x` in millimeters/sec and also to specify the rotating angle `y` in rad/sec for the bot to rotate. For example,

	differentialrobot_proxy->setSpeedBase(200, 0);

This line of code sets the bot speed to 200 mm/sec and rotating angle is 0 rad/sec construes that the bot will move forward at a speed of 200 mm/sec. Now that you know this function it becomes easier for us to make the bot to move in a square.

Open `specificworker.cpp` you can use any text editor of your choice to edit. the `void SpecificWorker::compute( )` is where all the code needs to be written and where robocomp will know to 'compute' the code. Using delay function and `differentialrobot_proxy->setSpeedBase(x , y);` we can write

	differentialrobot_proxy->setSpeedBase(200, 0); // move 200 mm/sec frwd
  	usleep(1000000); //delay 1 second
  	differentialrobot_proxy->setSpeedBase(10, 1.5707);  //rotate 90 degrees at a speed of 10 mm/sec
  	usleep(1000000);  // delay 1 second

Note that 1.5707 is nothing but 90 degrees in radians. Also keep in mind as it is rad/sec and not just radians changing the delay time to say 1.5 seconds would cause the bot to move for 135 degrees as 1.5707 rads/1.5sec. Now your `void SpecificWorker::compute()` should look like this


	void SpecificWorker::compute( )
	{
  
    	try
    	{
   
  		differentialrobot_proxy->setSpeedBase(200, 0); 
  		usleep(1000000);
  		differentialrobot_proxy->setSpeedBase(10, 1.5707); 
  		usleep(1000000);
           	
    	}
    	catch(const Ice::Exception &ex)
    	{
        	std::cout << ex << std::endl;   //If there is any error/exception display the same.
    	}
  
	}


Now save the file and build the component again

	cd path/to/firstcomp
	cmake .
	make

To simulate how this code behaves we utilize the robocomp's already available innermodel. Open a new tab in the terminal and execute

	cd robocomp/files/innermodel
	rcis simpleworld.xml

This would start the simulator with the innermodel simpleworld. You will see two windows one the entire view of the map or model and the other is the camera view of the bot which is in the center. NOw go back to the firstcomp tab in the terminal and let us run the component.

	bin/firstcomp --Ice.Config=etc/generic_config

The bot now moves in a square inside the innermodel simpleworld.
 
