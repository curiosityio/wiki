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

# Take photo using iOS device camera. Get photo from camera roll.

It is very similar to the above video capture, with a couple small changes to the UIImagePickerController configuration.

`Swift 3`

```
import UIKit
import MobileCoreServices
import Photos

class ViewController: UIViewController, UIImagePickerControllerDelegate, UINavigationControllerDelegate {

    @IBOutlet weak var takePhotoButton: UIButton!

    let imagePickerController = UIImagePickerController()

    override func viewDidLoad() {
        super.viewDidLoad()        
    }

    override func didReceiveMemoryWarning() {
        super.didReceiveMemoryWarning()
        // Dispose of any resources that can be recreated.
    }

    func imagePickerControllerDidCancel(_ picker: UIImagePickerController) {
        picker.dismiss(animated: true, completion: nil)
    }

    // image captured via camera.
    func imagePickerController(_ picker: UIImagePickerController, didFinishPickingMediaWithInfo info: [String : Any]) {
        let capturedImage = info[UIImagePickerControllerOriginalImage] as! UIImage // swiftlint:disable:this force_cast

        // saves to camera roll on phone. Maybe you don't need it? Maybe you can just send to API.
        PHPhotoLibrary.shared().performChanges({
            PHAssetChangeRequest.creationRequestForAsset(from: capturedImage)
        }) { saved, error in
            if saved {
                // photo saved to camera roll.
            } else {
                // do something with image.
            }
        }        

        self.dismiss(animated: true, completion: { _ in
            // do something with image.
        })
    }    

    @IBAction func getPhotoFromCameraRollButtonPressed(_ sender: AnyObject) {
        if UIImagePickerController.isSourceTypeAvailable(UIImagePickerControllerSourceType.photoLibrary) {
            let imagePicker = UIImagePickerController()
            imagePicker.delegate = self
            imagePicker.sourceType = UIImagePickerControllerSourceType.photoLibrary
            imagePicker.allowsEditing = false
            self.present(imagePicker, animated: true, completion: nil)
        } else {
            // error your device not able to access camera roll.
        }
    }

    // image got back from camera roll.
    func imagePickerController(picker: UIImagePickerController, didFinishPickingImage image: UIImage!, editingInfo: [NSObject : AnyObject]!) {
        self.dismiss(animated: true, completion: { _ in
            self.getPhotoCameraOrGalleryOnPhotoRetrieved(image)
        })
    }    

    @IBAction func takePhotoButtonPressed(_ sender: AnyObject) {
        if UIImagePickerController.isSourceTypeAvailable(.camera) && UIImagePickerController.isCameraDeviceAvailable(UIImagePickerControllerCameraDevice.front) {
            imagePickerController.allowsEditing = true
            imagePickerController.sourceType = UIImagePickerControllerSourceType.camera
            //            imagePickerController.mediaTypes = [kUTTypeJPEG as String] // Record movie (video with audio)
            imagePickerController.cameraCaptureMode = .photo
            imagePickerController.modalPresentationStyle = .fullScreen
            imagePickerController.cameraDevice = UIImagePickerControllerCameraDevice.front
            imagePickerController.delegate = self

            present(imagePickerController, animated: true, completion: nil)
        } else {
            // error. device not able to take pictures. Make sure to do this because emulators for example dont.
        }
    }

}
```

Next, add an item to Info.plist describing why you need to use the camera.

![](/docs/images/ios_camera_infoplist.png)
> [Credit image](http://useyourloaf.com/blog/privacy-settings-in-ios-10/)

*Note* Add the following entries to Info.plist depending on needs:

* If taking picture: `Privacy - Photo Library Usage Description`
* If saving picture to camera roll: `Privacy - Camera Usage Description` (same as getting picture from camera roll)
* If getting picture from camera roll: `Privacy - Camera Usage Description` (same as saving picture to camera roll)

![](/docs/images/ios_camera_show_from_plist.png)
> [Credit image](http://useyourloaf.com/blog/privacy-settings-in-ios-10/)
