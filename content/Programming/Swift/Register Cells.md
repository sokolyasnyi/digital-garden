---
title: Register Cells
draft: false
tags:
  - develop
  - cells
  - UICollectionView
  - UITableView
  - register
---
 Хороший вариант сокрытия реализации по ячейке в таблице или коллекции
```swift
protocol BannerCellControllerCellProtocol: AnyObject {
    func didTapHide(indexPath: IndexPath)
}

class BannerCellController: CellController {
   
    private var model: BannerModel?
   
    weak private var delegate: BannerCellControllerCellProtocol?
    
    init(model: BannerModel, delegate: BannerCellControllerCellProtocol?) {
        self.model = model
        self.delegate = delegate
    }
   
    static func configure(tableView: UITableView) {
        tableView.register(BannerCell.nib, forCellReuseIdentifier: BannerCell.cellIdentifier)
    }
    
    func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        let cell = tableView.dequeueReusableCell(withIdentifier: BannerCell.cellIdentifier, for: indexPath) as! BannerCell
        cell.configCell(item: model, indexPath: indexPath)
               
        cell.didTapHide = { [weak self] _ in
            self?.delegate?.didTapHide(indexPath: indexPath)
        }
        
        return cell
    }
    
}
```

Source: https://github.com/nikolaveljic64/example-project/blob/main/Example%20Project/App/Components/Feed/Feed/Cells/Banner/BanerCellController.swift
https://www.essentialdeveloper.com/articles/polymorphic-table-collectionviews-multiple-cell-types-without-if-else-switch-live-dev-mentoring

