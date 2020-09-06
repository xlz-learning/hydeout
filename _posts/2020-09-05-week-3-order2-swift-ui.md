---
layout: post
title: Tribe of mentors - Aug 30 - Sep 05
categories:
    - book
excerpt_separator:  <!--more-->
---

## [Project 13, part 5](https://www.hackingwithswift.com/100/swiftui/66)

### Customizing our filter using ActionSheet

```swift
private func loadImage() {
    guard let inputImage = inputImage else { return }
    let beginImage = CIImage(image: inputImage) // Create CIImage from UIImage
    currentFilter.setValue(beginImage, forKey: kCIInputImageKey) // Set input image to the filter
    applyProcessing() // Process image
}

func applyProcessing() {
    // Apply slider value differently for different type of filter input keys (Prevent crash due to different values are needed for different keys as well)
    let inputKeys = currentFilter.inputKeys
    if inputKeys.contains(kCIInputIntensityKey) { currentFilter.setValue(filterIntensity, forKey: kCIInputIntensityKey) }
    if inputKeys.contains(kCIInputRadiusKey) { currentFilter.setValue(filterIntensity * 200, forKey: kCIInputRadiusKey) }
    if inputKeys.contains(kCIInputScaleKey) { currentFilter.setValue(filterIntensity * 10, forKey: kCIInputScaleKey) }
    
    guard let outputImage = currentFilter.outputImage else { return } // Get output input with CIImage type
    
    if let cgimg = context.createCGImage(outputImage, from: outputImage.extent) { // Create CGImage fro CIImage
        let uiImage = UIImage(cgImage: cgimg) // Create UIImage from CIImage
        image = Image(uiImage: uiImage) // Save the transformed image to the image placeholder
        processedImage = uiImage // Save the processed image so it can be used later
    }
}

// Set currentFilter with filter from the method argument, then load image
func setFilter(_ filter: CIFilter) {
    currentFilter = filter
    loadImage()
}

// The following code is added to the ContentView VStack to support action sheet
.actionSheet(isPresented: $showingFilterSheet) { () -> ActionSheet in
    // Create ActionSheet with buttons to apply different filters
    ActionSheet(title: Text("Select a filter"), buttons: [
        .default(Text("Crystallize")) { self.setFilter(CIFilter.crystallize()) },
        .default(Text("Edges")) { self.setFilter(CIFilter.edges()) },
        .default(Text("Gaussian Blur")) { self.setFilter(CIFilter.gaussianBlur()) },
        .default(Text("Pixellate")) { self.setFilter(CIFilter.pixellate()) },
        .default(Text("Sepia Tone")) { self.setFilter(CIFilter.sepiaTone()) },
        .default(Text("Unsharp Mask")) { self.setFilter(CIFilter.unsharpMask()) },
        .default(Text("Vignette")) { self.setFilter(CIFilter.vignette()) },
        .cancel()
    ])
}
```

### Saving the filtered image using UIImageWriteToSavedPhotosAlbum()

- We need to add `Privacy - Photo Library Additions Usage Description` with value to the `Info.plist` before attempt to save images to photo library

```swift
// Create ImageSaver.swift to help store image and provide feedback upon image saving finished
import UIKit

class ImageSaver: NSObject {
    
    var onComplete: ((Result<(), Error>) -> Void)? // Completion handler that contains logic to be called for both success and failure cases
    
    // Method to save photo to album
    func writeToPhotoAlbum(image: UIImage) {
        UIImageWriteToSavedPhotosAlbum(image, self, #selector(saveError), nil)
    }
    
    // Objc method that gets triggered upon image finish saving with either success or error status (and detail)
    @objc func saveError(_ image: UIImage, didFinishSavingWithError error: Error?, contextInfo: UnsafeRawPointer) {
        if let error = error {
            onComplete?(.failure(error))
        } else {
            onComplete?(.success(()))
        }
    }
}
```

```swift
// Button is added to provide the image saving functionality to the SwiftUI view
Button("Save") {
    // Unwrap processedImage
    guard let processedImage = self.processedImage else { return }
    
    // Initialize image saver
    let imageSaver = ImageSaver()
    // Provide success/failure handler to the image saver
    imageSaver.onComplete = { result in
        switch result {
        case .failure(_):
            print("Save failed")
        case .success(_):
            print("Save successful")
        }
    }
    // Trigger image saving
    imageSaver.writeToPhotoAlbum(image: processedImage)
}
```

## [Project 13, part 4](https://www.hackingwithswift.com/100/swiftui/65)

```swift
// Other than the ImagePicker which was built in the previous part, evertyhing is here and explained
struct ContentView: View {
    @State private var image: Image?
    @State private var filterIntensity = 0.5
    @State private var showingImagePicker = false
    @State private var inputImage: UIImage?
    
    @State var currentFilter = CIFilter.sepiaTone()
    let context = CIContext()
    
    var body: some View {
        // Create Binding that returns filterIntensity when fetching
        // When setting the value, update filterIntensity and also trigger applyProcessing() method
        // This is needed since @State is struct, wrappedValue is nonmutating, we have to create a custom binding to provide our own code to run when the value is read or written
        // https://www.hackingwithswift.com/books/ios-swiftui/creating-custom-bindings-in-swiftui for more details
        let intensity = Binding<Double>(
            get: {
                self.filterIntensity
            },
            set: {
                self.filterIntensity = $0
                self.applyProcessing()
            }
        )
        
        return NavigationView {
            VStack {
                ZStack {
                    Rectangle()
                        .fill(Color.secondary)
                    
                    if image != nil {
                        image?.resizable().scaledToFit()
                    } else {
                        Text("Tap to select a picture")
                            .foregroundColor(.white)
                            .font(.headline)
                    }
                }
                .onTapGesture {
                    self.showingImagePicker = true // Tap to show image picker
                }
                
                HStack {
                    Text("Intensity")
                    Slider(value: intensity)
                }
                .padding(.vertical)
                
                HStack {
                    Button("Change Filter") {
                        // change filter
                    }
                    
                    Spacer()
                    
                    Button("Save") {
                        // save the picture
                    }
                }
            }
            .padding([.horizontal, .bottom])
            .navigationBarTitle("Instafilter")
            .sheet(isPresented: $showingImagePicker,
                   onDismiss: loadImage) { // When sheet is dismissed, call loadImage()
                ImagePicker(image: self.$inputImage) // Present ImagePicker in a sheet
            }
        }
    }
    
    private func loadImage() {
        guard let inputImage = inputImage else { return }
        let beginImage = CIImage(image: inputImage) // Create CIImage from UIImage
        currentFilter.setValue(beginImage, forKey: kCIInputImageKey) // Set input image to the filter
        applyProcessing() // Process image
    }
    
    func applyProcessing() {
        currentFilter.intensity = Float(filterIntensity) // Set filter intensity with filterIntensity value
        
        guard let outputImage = currentFilter.outputImage else { return } // Get output input with CIImage type
        
        if let cgimg = context.createCGImage(outputImage, from: outputImage.extent) { // Create CGImage fro CIImage
            let uiImage = UIImage(cgImage: cgimg) // Create UIImage from CIImage
            image = Image(uiImage: uiImage) // Save the transformed image to the image placeholder
        }
    }
}
```

## [Project 13, part 3](https://www.hackingwithswift.com/100/swiftui/64)

### Using coordinators to manage SwiftUI view controllers

```swift
struct ImagePicker: UIViewControllerRepresentable {
    @Binding var image: UIImage? // Create 2 way binding
    @Environment(\.presentationMode) var presentationMode // Use to dismiss the ImagePicker view
    
    // Required to be implemented if class Coordinator is defined
    func makeCoordinator() -> Coordinator {
        Coordinator(self) // Construct Coordinator using the initializer defined inside the Coordinator inner class
    }
    
    // Have to conform to the 3 classes to register delegation
    class Coordinator: NSObject, UIImagePickerControllerDelegate, UINavigationControllerDelegate {
        var parent: ImagePicker // Create reference between the Coordinator and the ImagePicker so value can be passed
        init(_ parent: ImagePicker) {
            self.parent = parent
        }
        
        func imagePickerController(_ picker: UIImagePickerController, didFinishPickingMediaWithInfo info: [UIImagePickerController.InfoKey : Any]) {
            // After use picks the image, fetch the image using the line below
            if let uiImage = info[.originalImage] as? UIImage {
                parent.image = uiImage //Save it to the image variable inside ImagePicker
            }
            
            parent.presentationMode.wrappedValue.dismiss() // Dismiss the UIImagePickerController
        }
    }
    
    
    func makeUIViewController(context: Context) -> UIImagePickerController {
        let picker = UIImagePickerController()
        picker.delegate = context.coordinator // Use the coordinator defined as delegate for UIImagePickerController
        return picker
    }
    
    func updateUIViewController(_ uiViewController: UIImagePickerController, context: Context) {}
}

// Inside ContentView SwiftUI view
// We need to create a @State private var inputImage: UIImage? and pass it to ImagePicker's image Binding
```

### How to save images to the user’s photo library

First we need to add `Privacy - Photo Library Additions Usage Description : ${Some text to explain why we want to access user's photo library}`The first time when we are trying to access user's photo library, an alert will show up to ask for user's permission

```swift
// We need some sort of class that inherits from NSObject in order to write to the photo library and read the response
class ImageSaver: NSObject {
    func writeToPhotoAlbum(image: UIImage) {
        UIImageWriteToSavedPhotosAlbum(image, self, #selector(saveError), nil)
    }
    
    @objc
    func saveError(_ image: UIImage, didFinishSavingWithError error: Error?, contextInfo: UnsafeRawPointer) {
        print("Save finished!")
    }
}

// If we want to save the image and read response, we can do the following
// Construct ImageSaver instance to access the writeToPhotoAlbum method
let imageSaver = ImageSaver()
imageSaver.writeToPhotoAlbum(image: inputImage)
```

## [Project 13, part 2](https://www.hackingwithswift.com/100/swiftui/63)

### Integrating Core Image with SwiftUI

Using filter from `CoreImage.CIFilterBuiltins`

First we need to import the following library

```swift
import CoreImage
import CoreImage.CIFilterBuiltins
```

Next we’ll create the context and filter. For this example we’re going to use a sepia tone filter, which applies a brown tone that makes a photo look like it was taken a long time ago.

So, replace the **`// more code to come`** comment with this:

```swift
let context = CIContext()
let currentFilter = CIFilter.sepiaTone()
```

We can now customize our filter to change the way it works. Sepia is a simple filter, so it only has two interesting properties: **`inputImage`** is the image we want to change, and **`intensity`** is how strongly the sepia effect should be applied, specified in the range 0 (original image) and 1 (full sepia).

So, add these two lines of code below the previous two:

```swift
currentFilter.inputImage = beginImage
currentFilter.intensity = 1
```

- Read the output image from our filter, which will be a **`CIImage`**. This might fail, so it returns an optional.
- Ask our context to create a **`CGImage`** from that output image. This also might fail, so again it returns an optional.
- Convert that **`CGImage`** into a **`UIImage`**.
- Convert that **`UIImage`** into a SwiftUI **`Image`**.

```swift
// get a CIImage from our filter or exit if that fails
guard let outputImage = currentFilter.outputImage else { return }

// attempt to get a CGImage from our CIImage
if let cgimg = context.createCGImage(outputImage, from: outputImage.extent) {
    // convert that to a UIImage
    let uiImage = UIImage(cgImage: cgimg)

    // and convert that to a SwiftUI image
    image = Image(uiImage: uiImage)
}
```

### Wrapping a UIViewController in a SwiftUI view

Since `UIImagePickerController` is a `UIKit` class. We need to wrap it inside a `View` to use it.

We need to create a wrapper view, in this case we are creating a view called `ImagePicker`

It needs to conform to `UIViewControllerRepresentable` and implement 2 methods.

- `makeUIViewController`
- `updateUIViewController`

We are only going to implement `makeUIViewController` for  now

```swift
func makeUIViewController(context: Context) -> UIImagePickerController {
    UIImagePickerController()
}
```

Once we have the above method implemented, we can use `ImagePicker` to display the image picker view as shown below

```swift
struct ContentView: View {
    @State private var image: Image?
    @State private var showingImagePicker = false

    var body: some View {
        VStack {
            image?
                .resizable()
                .scaledToFit()

            Button("Select Image") {
               self.showingImagePicker = true
            }
        }
        .sheet(isPresented: $showingImagePicker) {
            ImagePicker()
        }
    }
}
```