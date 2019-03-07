# App Store Mockup Notes

## TOC

- [App Store Mockup Notes](#app-store-mockup-notes)
  - [TOC](#toc)
  - [Notes to Organize](#notes-to-organize)
  - [TODO](#todo)
  - [Main Points](#main-points)
  - [Convenience Notes](#convenience-notes)
  - [Class Notes](#class-notes)
    - [UICollectionViewController](#uicollectionviewcontroller)
      - [Registering a cell](#registering-a-cell)
      - [Creating the cell](#creating-the-cell)
      - [Changing the spacing between each cell](#changing-the-spacing-between-each-cell)
      - [Add the equivalent of margins to a collectionViewCell](#add-the-equivalent-of-margins-to-a-collectionviewcell)
      - [Perform actions to a cell when it is tapped](#perform-actions-to-a-cell-when-it-is-tapped)
    - [UICollectionViewDelegateFlowLayout](#uicollectionviewdelegateflowlayout)
      - [Change the height and width of a cell](#change-the-height-and-width-of-a-cell)
    - [UI Animations](#ui-animations)
      - [Finding the exact coordinates of a cell](#finding-the-exact-coordinates-of-a-cell)
      - [Animating the cell to cover the whole view](#animating-the-cell-to-cover-the-whole-view)
      - [Removing the animated cell from the view](#removing-the-animated-cell-from-the-view)

## Notes to Organize

- If you are transforming something, using `.identity` will bring the view back to where it was originally
- Call `self.view.layoutIfNeeded()` after setting up your constraints and when performing animations
- When you are dealing with animations you will almost always have these global variables:

```swift
    var topConstraint: NSLayoutConstraint?
    var leadingConstraint: NSLayoutConstraint?
    var widthConstraint: NSLayoutConstraint?
    var heightConstraint: NSLayoutConstraint?
```

## TODO

- [ ] Understand generics better
- [ ] Understand completions better
- [ ] Understand self better
- [ ] Upload to GitHub

## Main Points

- Understand the self property
- Generics
- Private enum
- Putting stackviews in stackviews
- Convenience methods
- DispatchQueue.main.async
- Custom completions
- Using UIView() for spacing
- Dependency Injection
- Completions

## Convenience Notes

When I'm creating labels, images, buttons, etc... I would save a lot of time making a seperate extensions file and adding convenience initializers like this:

```swift
import UIKit

extension UILabel {
    convenience init(text: String, font: UIFont, numberOfLines: Int = 1) {
        self.init(frame: .zero)
        self.text = text
        self.font = font
        self.numberOfLines = numberOfLines
    }
}

extension UIImageView {
    convenience init(cornerRadius: CGFloat) {
        self.init(image: nil)
        self.layer.cornerRadius = cornerRadius
        self.clipsToBounds = true
        self.contentMode = .scaleAspectFill
    }
}

extension UIButton {
    convenience init(title: String) {
        self.init(type: .system)
        self.setTitle(title, for: .normal)
    }
}
```

## Class Notes

Discussed:

- UICollectionViewController
- UICollectionViewDelegateFlowLayout

### UICollectionViewController

#### Registering a cell

Inside of `viewDidLoad()` you want to call `collectionView.register(TodayCell.self, for cellWithReuseIdentifier: cellId)`

#### Creating the cell

```swift
    override func collectionView(_ collectionView: UICollectionView, cellForItemAt indexPath: IndexPath) -> UICollectionViewCell {
        let cell = collectionView.dequeueReusableCell(withReuseIdentifier: cellId, for: indexPath) as! TodayCell
        return cell
    }
```

#### Changing the spacing between each cell

```swift
    func collectionView(_ collectionView: UICollectionView, layout collectionViewLayout: UICollectionViewLayout, minimumLineSpacingForSectionAt section: Int) -> CGFloat {
        return 32
    }
```

#### Add the equivalent of margins to a collectionViewCell

```swift
    func collectionView(_ collectionView: UICollectionView, layout collectionViewLayout: UICollectionViewLayout, insetForSectionAt section: Int) -> UIEdgeInsets {
        return .init(top: 32, left: 0, bottom: 32, right: 0)
    }
```

#### Perform actions to a cell when it is tapped

```swift
    override func collectionView(_ collectionView: UICollectionView, didSelectItemAt indexPath: IndexPath) {
        print("Animate fullscreen somehow...")
    }
```

### UICollectionViewDelegateFlowLayout

#### Change the height and width of a cell

```swift
    func collectionView(_ collectionView: UICollectionView, layout collectionViewLayout: UICollectionViewLayout, sizeForItemAt indexPath: IndexPath) -> CGSize {
        return CGSize(width: view.frame.width - 64, height: 450)
    }
```

### UI Animations

#### Finding the exact coordinates of a cell

It is tricker than it sounds, you are going to need to do this inside `didSelectItemAt`

```swift
    // Use absolute coordinates to find the acutal x, y for a cell
    // Starting frame knows exactly where the cell is
    guard let startingFrame = cell.superview?.convert(cell.frame, to: nil) else { return }

    self.startingFrame = startingFrame
    redView.frame = startingFrame
```

#### Animating the cell to cover the whole view

```swift
    UIView.animate(withDuration: 0.7, delay: 0, usingSpringWithDamping: 0.7, initialSpringVelocity: 0.7, options: .curveEaseOut, animations: {
        redView.frame = self.view.frame
    }, completion: nil)
```

#### Removing the animated cell from the view

```swift
var startingFrame: CGRect?

@objc func handleRemoveRedView(gesture: UITapGestureRecognizer) {
    // Access starting frame
    UIView.animate(withDuration: 0.7, delay: 0, usingSpringWithDamping: 0.7, initialSpringVelocity: 0.7, options: .curveEaseOut, animations: {
        gesture.view?.frame = self.startingFrame ?? .zero
    }, completion: { _ in
        gesture.view?.removeFromSuperview()
    })
}
```
