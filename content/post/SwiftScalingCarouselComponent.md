+++
author = "Pete Smith"
date = "2017-02-08T20:48:26Z"

title = "iOS scaling carousel component in Swift"
description = ""

tags = [
    "swiftlang",
    "ios",
    "opensource"
]
categories = [
    "Swift",
    "iOS",
    "Opensource"
]

+++

I recently published [this](https://github.com/superpeteblaze/ScalingCarousel) library. It’s a custom UICollectionView component with carousel-style scrolling & scaling of cells. It’s used in Bikey, as seen below;

![ScalingCarousel in Bikey][scalingCarouselComponentImage1]
[scalingCarouselComponentImage1]: /images/posts/scalingCarouselComponentImage1.gif "ScalingCarousel in Bikey"

In this post, I describe the structure of the library, how it works, and how to use it.

---

### Library structure

ScalingCarousel consists of 3 Swift classes, shown below in order of relative importance (because what kind of person creates an unordered list! 😜);

1. ScalingCarouselView: A UICollectionView subclass, and the most interesting part. It does most of the heavy lifting. 
2. ScalingCarouselLayout: A UICollectionViewFlowLayout subclass, it handles sizing of cells, and collection view layout.
3. ScalingCarouselCell: A UICollectionViewCell subclass. It provides a method to scale itself.

---

### How it works

* When ScalingCarouselView is initialized, it adds an ‘invisible’ UIScrollView as a subview of it’s immediate superview. We say it’s ‘invisible’ as it won’t be visible to the user, and because it also allows it’s touch events to fall through to the underlying ScalingCarouselView. **This additional UIScrollView is the key to everything**, as it allows our ScalingCarouselCells to scroll correctly (more on this later). Adding the ‘invisible’ UIScrollView:

```
/// Add our 'invisible' scrollview
invisibleScrollView = UIScrollView(frame: bounds)
invisibleScrollView.translatesAutoresizingMaskIntoConstraints = false
invisibleScrollView.isPagingEnabled = true
invisibleScrollView.showsHorizontalScrollIndicator = false

/*
Disable user interaction on the 'invisible' scrollview,
This means touch events will fall through to the underlying UICollectionView
*/
invisibleScrollView.isUserInteractionEnabled = false

/// Set the scroll delegate to be the ScalingCarouselView
invisibleScrollView.delegate = self

/*
Now add the invisible scrollview's pan
gesture recognizer to the ScalingCarouselView
*/
addGestureRecognizer(invisibleScrollView.panGestureRecognizer)

/*
Finally, add the 'invisible' scrollview as a subview
of the ScalingCarousel's superview
*/
superview.addSubview(invisibleScrollView)

/*
Add constraints for height and top, relative to the
ScalingCarouselView
*/
invisibleScrollView.heightAnchor.constraint(equalTo: heightAnchor).isActive = true
invisibleScrollView.topAnchor.constraint(equalTo: topAnchor).isActive = true
```

* Our ScalingCarouselView has a variable property of type CGFloat named inset. This property enables us to set the horizontal ‘inset’ for our center carousel cell. **This property is also important**, as it also helps to ensure our ScalingCarouselView scrolls correctly (…more on this later). An inset value of zero means the center cell’s width is equal to that of the collection view, whereas a value of 20 means the center cells’s width is equal the the width of the ScalingCarouselView minus 40 (40 because the inset is applied on both the left and right sides of the center cell). We also add a property observer to inset, which calls a configureLayout() method. Our inset property:

```
/// Inset of the main, center cell
@IBInspectable public var inset: CGFloat = 0.0 {
   didSet {
      /*
      Configure our layout, and add more
      constraints to our invisible UIScrollView
      */
      configureLayout()
   }
}
```

* So what does the configureLayout() method do? Well, 2 things. First, using our inset value, it creates and ScalingCarouselLayout. Second, it adds more constraints to our ‘invisible’ UIScrollView. These constraints ensure that our ‘invisible’ UIScrollView is positioned in the center of our ScalingCarouselView, and also that it is the exact same size as each ScalingCarouselCell. **It’s very important** that our ‘invisible’ UIScrollView is positioned and sized correctly (yes, more on this later). Configuring:

```
// Create a ScalingCarouselLayout using our inset
collectionViewLayout = ScalingCarouselLayout(
withCarouselInset: inset)

/*
Only continue if we have a reference to
our 'invisible' UIScrollView
*/
guard let invisibleScrollView = invisibleScrollView else { return }

// Remove constraints if they already exist
invisibleWidthConstraint?.isActive = false
invisibleLeftConstraint?.isActive = false

/*
Add constrants for width and left postion
to our 'invisible' UIScrollView
*/
invisibleWidthConstraint = invisibleScrollView.widthAnchor.constraint(equalTo: widthAnchor, constant: -(2 * inset))
invisibleLeftConstraint =  invisibleScrollView.leftAnchor.constraint(equalTo: leftAnchor, constant: inset)

// Activate the constraints
invisibleWidthConstraint?.isActive = true
invisibleLeftConstraint?.isActive = true
```

* Using the inset, and overriding the UICollectionViewLayout prepare() method, the ScalingCarouselLayout sets the size of each ScalingCarouselCell using it’s itemSize property. It also sets up a few other common UICollectionView properties such as scrollDirection and isPagingEnabled:

```
override open func prepare() {
guard let collectionViewSize = collectionView?.frame.size else {    
      return }
   // Set itemSize based on total width and inset
   itemSize = collectionViewSize
   itemSize.width = itemSize.width - (inset * 2)

   // Set scrollDirection and paging
   scrollDirection = .horizontal
   collectionView?.isPagingEnabled = true
...
}
```

* Now, the contentSize. It’s important that the size of our ‘invisible’ UIScollView’s contentSize is updated whenever the size of our UICollectionView’s contentSize changes. To ensure this, we override the contentSize property in ScalingCarouselView in order to add a property observer. This allows us to set the UIScrollView’s contentSize relative to the UICollectionsView’s contentSize:

```
/// Override of the collection view content size to add an observer
override open var contentSize: CGSize {
   didSet {
      guard let dataSource = dataSource else { return }
      let numSections = dataSource.numberOfSections?(in: self) ?? 1

      // Calculate total number of items in collection view
      var numberItems = 0
      for i in 0..<numberSections {
         let numberSectionItems = dataSource.collectionView(self,
         numberOfItemsInSection: i)
            numberItems += numberSectionItem
      }

      /*
      Set the invisibleScrollView contentSize width based on
      number of items
      */
      let contentWidth = invisibleScrollView.frame.width *      
      CGFloat(numberItems)
      invisibleScrollView.contentSize = CGSize(width: contentWidth,
      height: invisibleScrollView.frame.height)
}
```

* **So, finally, how exactly does the ‘invisible’ UIScrollView we added help us?** Well, remember we constrained the ‘invisible’ UIScrollView such that it is positioned in the center of our ScalingCarouselView, and it’s size matches that our our ScalingCarouselCells. Remember also that we set the ‘invisible’ UIScrollView’s isPagingEnabled property to true, and we set the UIScrollView’s delegate to be the ScalingCarousel. By combining all of these, and by implementing the scrollViewDidScroll(:) delegate method, we can now programmatically scroll our ScalingCarouselView by the exact amount paged/scrolled by the ‘invisible’ UIScrollView. **This is how** we keep each center cell exactly centered! Programmatically scrolling by setting our ScalingCarouselView’s contentOffset to match that of our ‘invisible’ UIScrollView’s contentOffset:

```
public func scrollViewDidScroll(_ scrollView: UIScrollView) {
/*
   Move the ScalingCarousel based on the
   contentOffset of the 'invisible' UIScrollView
   */
   contentOffset = invisibleScrollView.contentOffset

   // Also, this is where we scale our cells
   for cell in visibleCells
      if let infoCardCell = cell as? ScalingCarouselCell {
         infoCardCell.scale(withCarouselInset: inset)
      }
   }
}
```

* One last thing. The scaling of our cells. As seen above, each ScalingCarouselCell is scaled as the user scrolls. The scaling is based on two main values — the absolute position of the cell, and the ScalingCarouselView inset. Using these, we calculate the cell scale value (and alpha value) as follows:

```
open func scale(withCarouselInset carouselInset: CGFloat,
   scaleMinimum: CGFloat = 0.9) {
   // Ensure we have a superView, and mainView
   guard let superview = superview,
      let mainView = mainView else { return }

   // Get our absolute origin value
   let originX = superview.convert(frame, to: nil).origin.x

   // Calculate our actual origin.x value using our inset
   let originXActual = originX - carouselInset
   let width = frame.size.width

   // Calculate our scale values
   let scaleCalculator = fabs(width - fabs(originXActual))
   let percentageScale = (scaleCalculator/width)
  let scaleValue = scaleMinimum
      + (percentageScale/InternalConstants.scaleDivisor)
   let alphaValue = InternalConstants.alphaSmallestValue
      + (percentageScale/InternalConstants.scaleDivisor)
   let affineIdentity = CGAffineTransform.identity

   // Scale our mainView and set it's alpha value
   mainView.transform = affineIdentity.scaledBy(x: scaleValue, y:    
      scaleValue)
   mainView.alpha = alphaValue

   // ..also..round the corners
   mainView.layer.cornerRadius = 20
}
```

*Note: When describing how ScalingCarousel works, I left out some of the…less-interesting parts of the code. But should you be interested in every detail, you can always check out the [source](https://github.com/superpeteblaze/ScalingCarousel).*

---

### How to use ScalingCarousel

ScalingCarousel can be added via both storyboard/xib and code, as described below.

## Storyboard
* Add a UICollectionView to your view, and change the type to ScalingCarouselView

* In the attributes inspector, set the desired carousel inset

* Set your UIViewController as the collection view datasource and implement the standard UICollectionViewDatasource methods in your view controller

* Set your UIViewController as the collection view delegate and implement the UIScrollViewDelegate method scrollViewDidScroll(:). In this method, call the didScroll() method of ScalingCarouselView

* Create a custom UICollectionViewCell which inherits from ScalingCarouselCell, and set the cell type to your custom cell type in the storyboard

* Add a view to the cell’s content view, and connect this via the Connections Inspector (in Interface builder) to the cell’s mainView IBOutlet. This property is declared in ScalingCarouselCell. You should add any cell content to this view.

## Code
* Create a custom UICollectionViewCell which inherits from ScalingCarouselCell. Initialize the mainView property, which is declared in ScalingCarouselCell:

```
override init(frame: CGRect) {
  super.init(frame: frame)

  // Initialize the mainView property and add it to the cell's
  // contentView
  mainView = UIView(frame: contentView.bounds)
  contentView.addSubview(mainView)
}
```

* Create and add a ScalingCarouselView to your view, and implement the standard UICollectionViewDatasource methods in your view controller:

```
// Create our carousel
let scalingCarousel = ScalingCarouselView(withFrame: frame, andInset: 20)
scalingCarousel.dataSource = self
scalingCarousel.translatesAutoresizingMaskIntoConstraints = false

// Register our custom cell for dequeueing
scalingCarousel.register(Cell.self, forCellWithReuseIdentifier: "cell")

// Add our carousel as a subview        
view.addSubview(scalingCarousel)

// Add Constraints
scalingCarousel.widthAnchor.constraint(equalTo: view.widthAnchor, multiplier: 1).isActive = true
scalingCarousel.heightAnchor.constraint(equalToConstant: 300).isActive = true
scalingCarousel.leadingAnchor.constraint(equalTo: view.leadingAnchor).isActive = true
scalingCarousel.topAnchor.constraint(equalTo: view.topAnchor, constant: 50).isActive = true
```

* Set your UIViewController as the collection view delegate and implement the UIScrollViewDelegate method scrollViewDidScroll(:). In this method, call the didScroll() method of ScalingCarouselView

---

That’s it! If you think you might find the ScalingCarousel component useful, or if you want to fix/improve/whatever it, head over to [Github](https://github.com/superpeteblaze/ScalingCarousel).

Please also feel free to share, like, etc. this post.
