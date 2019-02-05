This file describes the work done and steps to perform test on the filters package.

"https://github.com/swatifulzele/filters/tree/ros2-crystal"

ROS2 Migration Changes

	Basic design and concept is same as per ros.
	Work is done by referring ROS1 lunar-devel branch filters package folder.Following are the changes: 
		1. Migrated all header and source files into ROS2 style 
		2. Migrated yaml files into ROS2 style
		3. Migrated .launch files into launch.py in ROS2 style
		4. Migrated CMakeLists.txt and package.xml in ROS2 style

Design changes:

	1. The parameters :
		Parameters for filters are taken from yaml file only. So filter type is configured using node paramter 
		and corresponding code and logical parsing changes have been added to devired class of filters. 

	2. The yaml file is redesigned so as to configure multiple filters in sequence. This is done by simply adding "filter1" and "filter2" keys to identify parameters for different type of filters in the dictionary of yaml file. So to configure a filter type the parameter is set in the yaml as shown below:
		
		MultiChannelMedianFilterFloat5:
			ros__parameters:
				filter1:
					name: median_test
					type: filters/MultiChannelMedianFilterFloat
					params: {number_of_observations: 5}  
		TwoFilters:
			ros__parameters:
				filter1:
					name: median_test_unique
					type: filters/MultiChannelMedianFilterDouble
					params: {number_of_observations: 5}
				filter2:
					name: median_test2
					type: filters/MultiChannelMedianFilterDouble
					params: {number_of_observations: 5}      

	 3. Support for the yaml file related to different dictionary style is provided through logical changes in the design and is more generic now.For example:
	array_filter_chain:
		ros__parameters:
			filter1:
				type: laser_filters/LaserArrayFilter
				name: laser_median_filter
				params: 
					range_filter_chain:
						name: median_2
						type: filters/MultiChannelMeanFilterFloat 
						params:{number_of_observations: 3}
					intensity_filter_chain:
						name: median_2
						type: filters/MultiChannelMeanFilterFloat
						params:{number_of_observations: 3}		
                    
	4. Xmlrpc variables replaced by string types variables and required modification/changes incorporated. 
	5. The " confugure()" function for inherited classes i.e. implementation of different type of filters is maintain as per ros1 design.(Earlier it was get_parameter(param_name, node) with two arguments). 

Build proccedure and testing

	1. Get pacakge at local system 

		1.1 # mkdir -p filters_package/src

		1.2 # cd filters_package/src
	
		1.3 # git clone https://github.com/swatifulzele/filters.git -b ros2-crystal 

		1.4 # source /opt/ros/crystal/setup.bash 

		

	2. Build the package

		2.1 # cd ../
		2.2 # colcon build

	3. TESTING:
	
		Here launch files are used independently.Following are the steps to run the test cases independently:
 
		1. Set the path  

			1.1 # source ./install/setup.bash 

                2. To run Test cases related to the mean filter use following command:
                        ros2 launch filters test_mean.launch.py

		3. To run Test cases related to the median filter use following command:
                        ros2 launch filters test_median.launch.py

		4. To run Test cases related to the test_params use following command:
                        ros2 launch filters test_params.launch.py

		5. To run Test cases related to the transfer function filter use following command:
                        ros2 launch filters test_transfer_function.launch.py

		6. To run Test cases related to the filter chain  use following command:
                        ros2 launch filters test_chain.launch.py

NOTE:- 
	1. Colcon test does not work as launch.py files can not be executed/added with CMakeLists.txt as of now.
	2. Each test/launch.py files have been tested independently.
	3. To use this package in any application, user will have to follow the design of yaml file as mentioned in the design changes above.  
