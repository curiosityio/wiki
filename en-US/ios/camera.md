---
name: Camera
---

# Record video using iOS device camera

```
import UIKit
import MobileCoreServices
import Photos

class ViewController: UIViewController, UIImagePickerControllerDelegate, UINavigationControllerDelegate {

    @IBOutlet weak var recordVideoButton: UIButton!

    let maximumSecondsLengthRecordVideo = 10.0

    let imagePickerController = UIImagePickerController()

    override func viewDidLoad() {
        super.viewDidLoad()

        imagePickerController.delegate = self
    }

    func imagePickerControllerDidCancel(picker: UIImagePickerController) {
        picker.dismissViewControllerAnimated(true, completion: nil)
    }

    func imagePickerController(picker: UIImagePickerController, didFinishPickingMediaWithInfo info: [String : AnyObject]) {
        let urlOfVideo = info[UIImagePickerControllerMediaURL] as? NSURL

        // saves to camera roll on phone. Maybe you don't need it? Maybe you can just send to API.
        PHPhotoLibrary.sharedPhotoLibrary().performChanges({
            PHAssetChangeRequest.creationRequestForAssetFromVideoAtFileURL(urlOfVideo!)
        }) { saved, error in
            if saved {
                // video saved to camera roll.
            }
        }

        dismissViewControllerAnimated(true, completion: nil)
    }

    @IBAction func recordVideoButtonPressed(sender: AnyObject) {
        // below if statement says "make sure a front facing camera is available on device"
        if UIImagePickerController.isSourceTypeAvailable(.Camera) && UIImagePickerController.isCameraDeviceAvailable(UIImagePickerControllerCameraDevice.Front) {
            imagePickerController.allowsEditing = false
            imagePickerController.sourceType = UIImagePickerControllerSourceType.Camera
            imagePickerController.mediaTypes = [kUTTypeMovie as String] // Record movie (video with audio)
            imagePickerController.cameraCaptureMode = .Video
            imagePickerController.modalPresentationStyle = .FullScreen
            imagePickerController.videoMaximumDuration = maximumSecondsLengthRecordVideo
            imagePickerController.videoQuality = UIImagePickerControllerQualityType.TypeMedium
            imagePickerController.cameraDevice = UIImagePickerControllerCameraDevice.Front

            presentViewController(imagePickerController, animated: true, completion: nil)
        } else {
            // show alert dialog saying no front facing camera on device, cannot take video.
        }
    }

}
```

# Take photo using iOS device camera

It is very similar to the above video capture, with a couple small changes to the UIImagePickerController configuration.

```
import UIKit
import MobileCoreServices
import Photos

class ViewController: UIViewController, UIImagePickerControllerDelegate, UINavigationControllerDelegate {

    @IBOutlet weak var takePhotoButton: UIButton!

    let maximumSecondsLengthRecordVideo = 10.0

    let imagePickerController = UIImagePickerController()

    override func viewDidLoad() {
        super.viewDidLoad()

        imagePickerController.delegate = self
    }

    override func didReceiveMemoryWarning() {
        super.didReceiveMemoryWarning()
        // Dispose of any resources that can be recreated.
    }

    func imagePickerControllerDidCancel(picker: UIImagePickerController) {
        picker.dismissViewControllerAnimated(true, completion: nil)
    }

    func imagePickerController(picker: UIImagePickerController, didFinishPickingMediaWithInfo info: [String : AnyObject]) {
        let capturedImage = info[UIImagePickerControllerOriginalImage] as! UIImage

        // saves to camera roll on phone. Maybe you don't need it? Maybe you can just send to API.
        PHPhotoLibrary.sharedPhotoLibrary().performChanges({
            PHAssetChangeRequest.creationRequestForAssetFromImage(capturedImage)
        }) { saved, error in
            if saved {
                // photo saved to camera roll.
            }
        }

        dismissViewControllerAnimated(true, completion: nil)
    }

    @IBAction func takePhotoButtonPressed(sender: AnyObject) {
        if UIImagePickerController.isSourceTypeAvailable(.Camera) && UIImagePickerController.isCameraDeviceAvailable(UIImagePickerControllerCameraDevice.Front) {
            imagePickerController.allowsEditing = false
            imagePickerController.sourceType = UIImagePickerControllerSourceType.Camera
            imagePickerController.mediaTypes = [kUTTypeImage as String] // Record movie (video with audio)
            imagePickerController.cameraCaptureMode = .Photo
            imagePickerController.modalPresentationStyle = .FullScreen
            imagePickerController.cameraDevice = UIImagePickerControllerCameraDevice.Front

            presentViewController(imagePickerController, animated: true, completion: nil)
        } else {
            // show alert dialog saying no camera on device, cannot take video.
        }
    }

}
```
