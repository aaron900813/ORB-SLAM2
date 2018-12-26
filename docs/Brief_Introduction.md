# A Brief Introduction Based On Monocular Video Example

First of all, we instantiate `ORB_SLAM2::System`  
&emsp;1.load ORBVocabulary that was created offline  
&emsp;2.create keyframedatabase instance  
&emsp;3.create map instance  
&emsp;4.create framedrawer and mapdrawer instance  
&emsp;5.create track instance and create three(LocalMapping, LoopClosing, Viewer) instances and threads

then, when user first call `GrabImageMonocular`
```
cv::Mat ORB_SLAM2::Tracking::GrabImageMonocular(const cv::Mat & im, const double & timestamp)
```
system will use constructor create one `frame` instance named `mCurrentFrame` for store ** current matched points, frame pose, camera inlier matrix and etc **,
and extract ORB Feature points meanwhile.   
after create `mCurrentFrame`, call track
```
cv::Mat ORB_SLAM2::Tracking::track()
```
***`mstate` is a member variable of ORB_SLAM2::Tracking.***  
if `mstate` is `NO_IMAGES_YET` set it to be `NOT_INITIALIZED`
if `mpInitializer` is `NULL` and `mvKeys` of `mCurrentFrame` is greater than **100**, use copy constructor base on `mCurrentFrame` create two frame named `mInitialFrame` `mLastFrame`, use `Initializer` constructor create `mpInitializer` and return. else do nothing.
```
cv::Mat ORB_SLAM2::Initializer::Initializer(const Frame &ReferenceFrame, float sigma=1.0, int iterations=200)
```

so now, we have initial system for receiving second picture to calculate homography matrix,
if `mpInitializer` isn't `NULL` but `mvKeys` of `mCurrentFrame` is smaller than **100**, set `mpInitializer` to `NULL` that indicate last two frame is not safe to initial camera pose.
so if `mvKeys` of `mCurrentFrame` is greater than **100**, then we get two frame to initial camera pose.

then we use `ORBmatcher` is search match pairs, pair two points if they close to each other, when got at least 100 pairs, use these pairs to calculate H and S models scores. and then we Got T. and insert all match points(convert them to world coordinate) to `map`.

after initiation, we use `TrackReferenceKeyFrame` to track camera, search match points between reference key frame and current frame. and use Optimizer::PoseOptimization to get a better T.

once `TrackReferenceKeyFrame` return true, every times we got a frame, use `TrackWithMotionModel` to track camera, we assume camera move at a constant velocity, so current frame pose is **velocity * lastFrame pose**, so use this pose to search match points. if these two frames do not have 20 match points, we use a wilder search window, then if either, return false, else use Optimizer::PoseOptimization to get a better T. if `TrackWithMotionModel` return false, so we use `TrackReferenceKeyFrame` to search match points. if `TrackReferenceKeyFrame` also return false, then we lost camera. we use `Relocalization` to re-localize camera. the camera should go back to some scene that has ever met.

