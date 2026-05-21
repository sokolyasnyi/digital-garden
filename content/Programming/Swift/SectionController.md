---
title: SectionController
draft: false
tags:
  - develop
  - diffable
  - dataSource
  - UICollectionView
  - cells
---
 ```swift
 
 public struct CollectionViewSectionController {
    
    public enum Category: Equatable {
        case list
        case carousel(height: CGFloat?)
        case grid
        case tags
    }
    
    public let id: AnyHashable
    public let category: Category
    public let header: SupplementaryViewController?
    public let controllers: [CollectionViewCellController]
    public let footer: SupplementaryViewController?
    
    public init(id: AnyHashable = UUID(), category: Category, header: SupplementaryViewController? = nil, controllers: [CollectionViewCellController], footer: SupplementaryViewController? = nil) {
        self.id = id
        self.category = category
        self.header = header
        self.controllers = controllers
        self.footer = footer
    }
}

extension CollectionViewSectionController: Equatable {
    public static func == (lhs: CollectionViewSectionController, rhs: CollectionViewSectionController) -> Bool {
        return lhs.id == rhs.id
            && lhs.controllers == rhs.controllers
    }
}

extension CollectionViewSectionController: Hashable {
    public func hash(into hasher: inout Hasher) {
        hasher.combine(id)
        hasher.combine(controllers)
    }
}
 ```


```swift
public struct CollectionViewCellController {
    public let id: AnyHashable
    public let dataSource: UICollectionViewDataSource
    public let delegate: UICollectionViewDelegateFlowLayout?
    public let dataSourcePrefetching: UICollectionViewDataSourcePrefetching?
    
    public init(id: AnyHashable = UUID(), _ dataSource: UICollectionViewDataSource) {
        self.id = id
        self.dataSource = dataSource
        self.delegate = dataSource as? UICollectionViewDelegateFlowLayout
        self.dataSourcePrefetching = dataSource as? UICollectionViewDataSourcePrefetching
    }
}

extension CollectionViewCellController: Equatable {
    public static func == (lhs: CollectionViewCellController, rhs: CollectionViewCellController) -> Bool {
        return lhs.id == rhs.id
    }
}

extension CollectionViewCellController: Hashable {
    public func hash(into hasher: inout Hasher) {
        hasher.combine(id)
    }
}
```