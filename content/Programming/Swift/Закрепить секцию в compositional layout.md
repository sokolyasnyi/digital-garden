---
title: Закрепить секцию в compositional layout
draft: false
tags:
  - develop
  - compositional
  - layout
  - UICollectionView
---
Чтобы закрепить секцию ее необходимо зарегистрировать, как boundarySupplementaryItems. 

```swift
let headerFooterSize = NSCollectionLayoutSize(widthDimension: .fractionalWidth(1.0), heightDimension: .estimated(44)) 

let header = NSCollectionLayoutBoundarySupplementaryItem(layoutSize: headerFooterSize, elementKind: "header", alignment: .top)

let footer = NSCollectionLayoutBoundarySupplementaryItem(layoutSize: headerFooterSize, elementKind: "footer", alignment: .bottom) 

let config = UICollectionViewCompositionalLayoutConfiguration() config.interSectionSpacing = 20
config.scrollDirection = .horizontal
config.boundarySupplementaryItems = [header, footer]
```

Source: https://developer.apple.com/documentation/uikit/uicollectionviewcompositionallayoutconfiguration
Source: https://medium.com/@hdmdhr/pin-header-to-the-top-in-a-multi-sections-uicollectionview-9f1103f247a5
