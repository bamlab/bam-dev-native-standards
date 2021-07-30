# CIFilter

[cifilter.io](https://cifilter.io)

## Barcode and QRCode

You can generate a barcode / a QRcode with CI Filter

The code below needs to be run in a playground.

``` swift
import UIKit
import PlaygroundSupport

class MyViewController : UIViewController {
  override func loadView() {
    let view = UIView()
    view.backgroundColor = .white
    
    let barcode = generateBarcode(from: "Hello world")
    view.addSubview(UIImageView(image: barcode))
    self.view = view
  }
  
  
  func generateBarcode(from string: String) -> UIImage? {
    let data = string.data(using: String.Encoding.ascii)
    
    if let filter = CIFilter(name: "CICode128BarcodeGenerator") { // (1)
      filter.setValue(data, forKey: "inputMessage")
      if let output = filter.outputImage {
        return UIImage(ciImage: output) // (2)
      }
    }
    return nil
  }
}
PlaygroundPage.current.liveView = MyViewController() // (3)
```

1. See cifilter.io for other code generator
2. Create image from filter output
3. Render the view controller in the playground

## Play a transparent video

Take an image > filter the alpha channel.
