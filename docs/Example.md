# Some Example
## 1. Monocular Video
```
/**
* This file is part of ORB-SLAM2.
*
* Copyright (C) 2014-2016 Raúl Mur-Artal <raulmur at unizar dot es> (University of Zaragoza)
* For more information see <https://github.com/raulmur/ORB_SLAM2>
*
* ORB-SLAM2 is free software: you can redistribute it and/or modify
* it under the terms of the GNU General Public License as published by
* the Free Software Foundation, either version 3 of the License, or
* (at your option) any later version.
*
* ORB-SLAM2 is distributed in the hope that it will be useful,
* but WITHOUT ANY WARRANTY; without even the implied warranty of
* MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the
* GNU General Public License for more details.
*
* You should have received a copy of the GNU General Public License
* along with ORB-SLAM2. If not, see <http://www.gnu.org/licenses/>.
*/


#include<iostream>
#include<algorithm>
#include<fstream>
#include<chrono>

#include<opencv2/core/core.hpp>

#include<System.h>

using namespace std;

int main(int argc, char **argv)
{

    cv::Mat Tcw = cv::Mat::eye(4,4,CV_32F);
    std::cout << Tcw << std::endl;
    if(argc != 4)
    {
        cerr << endl << "Usage: ./mono_tum path_to_vocabulary path_to_settings path_to_video" << endl;
        return 1;
    }

    // Create SLAM system. It initializes all system threads and gets ready to process frames.
    ORB_SLAM2::System SLAM(argv[1],argv[2],ORB_SLAM2::System::MONOCULAR,true);

    cv::VideoCapture vid(argv[3]);
    // Main loop
    double ttrack_sum = 0;
    int nImages = 0;
    cv::Mat im;
    for(;;)
    {
        // Read image from file
        vid >> im;
        if(im.empty())
        {
            break;
        }
        // cv::imwrite("ori.jpeg", im);
        /*you should change resolution to your calibration resolution*/
        cv::resize(im, im, cv::Size(960, 544));
        // matrix transpose
        transpose(im, im); 
        //0: flip x； >0: flip y； <0: flip x and y
        flip(im, im, 1);

#ifdef COMPILEDWITHC11
        std::chrono::steady_clock::time_point t1 = std::chrono::steady_clock::now();
#else
        std::chrono::monotonic_clock::time_point t1 = std::chrono::monotonic_clock::now();
#endif

        // Pass the image to the SLAM system
        SLAM.TrackMonocular(im, nImages++);

#ifdef COMPILEDWITHC11
        std::chrono::steady_clock::time_point t2 = std::chrono::steady_clock::now();
#else
        std::chrono::monotonic_clock::time_point t2 = std::chrono::monotonic_clock::now();
#endif

        double ttrack= std::chrono::duration_cast<std::chrono::duration<double> >(t2 - t1).count();

        ttrack_sum += ttrack;
        if(ttrack < 0.04)
            usleep((0.04 - ttrack)*1e6);
    }

    getchar();
    // Stop all threads
    SLAM.Shutdown();

    cout << "-------" << endl << endl;
    cout << "mean tracking time: " << ttrack_sum/nImages << endl;

    // Save camera trajectory
    SLAM.SaveKeyFrameTrajectoryTUM("KeyFrameTrajectory.txt");

    return 0;
}
```
![mono_video.cpp call graph](../html/mono__video_8cc_a3c04138a5bfe5d72780bb7e82a18e627_cgraph.gif)

