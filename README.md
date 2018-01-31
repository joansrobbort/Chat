
var selectedFeedBeanToDelete : FeedBean!
    var selectedIndex = 0

==========================================================
func FeedLikeSend() {
        SVProgressHUD.show(withStatus: "Loding...")
        if let USerID = Pref.getObjectForKey(kUserBean) as? UserBean{
            let param = ["feedId" : "\(selectedFeedBeanToDelete.feedId)",
                         "userId" :"\(USerID.id)"] as [String : Any]
            
            print(param)
            
            ServerCall.sharedInstance.requestWithUrlAndParameters(.http_POST, urlString: URL_FEEDLIKE,parameters: param as [String : AnyObject], delegate: self, name: .FeedLikes)
        }
    }
===================================================
 if name == .FeedLikes {
            print("\n\n\n SUCCESS FeedLikes........... \n \(responseObject)")
            if let Dicdata = responseObject as? NSDictionary{
                arrayFeedBeans[selectedIndex].status = TO_INT(Dicdata["status"]! as AnyObject)
                if arrayFeedBeans[selectedIndex].status == 0 &&
                    arrayFeedBeans[selectedIndex].totalLike > 0 {
                    arrayFeedBeans[selectedIndex].totalLike -= 1
                } else if arrayFeedBeans[selectedIndex].status == 1 {
                    arrayFeedBeans[selectedIndex].totalLike += 1
                }
                
                DispatchQueue.main.async {
                    
                    let idxPath = IndexPath(row: self.selectedIndex, section: 0)
                    let cell = self.TableView.cellForRow(at: idxPath) as! MainMenuCell
                    let bean = self.arrayFeedBeans[self.selectedIndex]
                    if bean.status == 0 {
                        cell.BtnLike.setTitle("Like", for:.normal)
                    }
                    else{
                        cell.BtnLike.setTitle("UnLike", for:.normal)
                    }
                    
                    cell.lbllike.text = String(bean.totalLike) + "  Likes"
                    
//                    let anIndexPath = IndexPath(row: self.selectedIndex, section: 0)
//
//                    self.TableView.reloadRows(at: [anIndexPath], with: .none)
                    
                }
            }
        }
===================================================
=======================================================================================================
ar application in swift
https://www.raywenderlich.com/146436/augmented-reality-ios-tutorial-location-based-2
https://itunes.apple.com/in/app/augment-3d-augmented-reality/id506463171?mt=8
https://itunes.apple.com/us/app/stack-ar/id1269638287?mt=8
https://itunes.apple.com/us/app/stack-ar/id1269638287?mt=8
https://itunes.apple.com/us/app/mountains-ar-the-augmented-reality/id884969067?mt=8
https://itunes.apple.com/us/app/roomle-3d-ar-room-planner/id732050356?mt=8
https://itunes.apple.com/us/app/ikea-place/id1279244498?mt=8
https://www.theverge.com/2017/9/20/16339006/apple-ios-11-arkit-ikea-place-ar-app
https://9to5mac.com/2017/09/20/best-ar-arkit-apps-ios-11/
https://www.engadget.com/2017/09/12/ikea-place-augmented-reality-app/
https://itunes.apple.com/us/app/find-my-car-with-ar/id1065080375?mt=8
https://deepknowhow.com/2013/04/04/top-20-augmented-reality-apps-for-android-and-iphoneipad-users/










//  ViewController.swift
//  ChatViewDemo
//
//  Created by Vimal on 23/12/17.
//  Copyright © 2017 Vimal. All rights reserved.
//
extension String {
    func trim() -> String {
        return self.trimmingCharacters(in: CharacterSet.whitespacesAndNewlines)
    }
}


import UIKit

class ViewController: UIViewController,UITableViewDelegate,UITableViewDataSource,UITextViewDelegate {

    
    //MARK:-Outler ViewController
    @IBOutlet weak var viewHeight: NSLayoutConstraint!
    @IBOutlet weak var viewBotteam: NSLayoutConstraint!
    @IBOutlet weak var lblplaceholder: UILabel!
    @IBOutlet weak var viewheightConstrain: NSLayoutConstraint!
    @IBOutlet weak var txtChatMsg: UITextView!
    @IBOutlet weak var btnImage: UIButton!
    @IBOutlet weak var tblViewController: UITableView!
   var ArrayComment = [String]()
    
    //MARK:-ViewController LifeCycle
    override func viewDidLoad() {
        super.viewDidLoad()
        txtChatMsg.delegate = self
        txtChatMsg.enablesReturnKeyAutomatically = true
        lblplaceholder.sizeToFit()
        txtChatMsg.addSubview(lblplaceholder)
        lblplaceholder.isHidden = !txtChatMsg.text.isEmpty
        self.hideKeyboardWhenTappedAround()
    }
    
    func hideKeyboardWhenTappedAround() {
        let tap: UITapGestureRecognizer = UITapGestureRecognizer(target: self, action: #selector(ViewController.dismissKeyboard))
        view.addGestureRecognizer(tap)
    }
    @objc func dismissKeyboard() {
        view.endEditing(true)
        self.viewBotteam.constant = -218
    }
    //MARK:- TextFieldViewDelegate
    func textViewDidEndEditing(_ textView: UITextView) {

        self.viewBotteam.constant = -219
        self.view.layoutIfNeeded()
    }
    func textViewDidChange(_ textView: UITextView) {
        
        if textView.text.trim() == "" && txtChatMsg.text.trim() == "\n" {
            txtChatMsg.text = ""
            return
        }
        lblplaceholder.isHidden = !txtChatMsg.text.isEmpty
        
        let attributes = [NSAttributedStringKey.font: UIFont.systemFont(ofSize: 13)]
        
        let boundingRect = (self.txtChatMsg.text as NSString).boundingRect(with: CGSize(width:CGFloat(self.txtChatMsg.frame.size.width), height:CGFloat(70)), options: .usesLineFragmentOrigin, attributes: attributes, context: nil)
        
        let messageHeight: CGFloat = boundingRect.size.height
        
        //self.keypadView.needsUpdateConstraints()
        self.txtChatMsg.needsUpdateConstraints()
        self.viewheightConstrain.constant = messageHeight + 20
        self.viewHeight.constant = messageHeight + 35
        
    }
    
    func textViewDidBeginEditing(_ textView: UITextView) {
        self.viewBotteam.constant = -219
        
        DispatchQueue.main.async {
            self.tblViewController.reloadData()
            if self.ArrayComment.count > 0 {
                let lastIndexPath = IndexPath(row: self.ArrayComment.count - 1 , section: 0)
                self.tblViewController.scrollToRow(at: lastIndexPath, at: .bottom, animated: false)
            }
        }
        
        return
    }
    func textViewShouldEndEditing(_ textView: UITextView) -> Bool {
        return true
    }
    
    //MARK :- TableView method
    func tableView(_ tableView: UITableView, heightForRowAt indexPath: IndexPath) -> CGFloat {
        return UITableViewAutomaticDimension
    }
    func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        return ArrayComment.count
    }
    func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        let Cell:leftCell = tableView.dequeueReusableCell(withIdentifier:"leftCell")as! leftCell
        Cell.leftlblMsg.text = ArrayComment[indexPath.row]
        return Cell
    }
    
    
    //MARK:-ViewController Action
    @IBAction func TapToImogie(_ sender: Any) {
        
    }
    @IBAction func TapToDone(_ sender: Any) {
        self.view.endEditing(true)
        if txtChatMsg.text.trim() == ""{
            
            self.txtChatMsg.text = ""
            self.viewBotteam.constant = 0
            self.viewheightConstrain.constant = 40
            self.viewHeight.constant = 50
            self.lblplaceholder.isHidden = false
            
            return
            
        }
        else{
            let msg = txtChatMsg.text.trim()
            self.ArrayComment.append(msg)
//            if msg.characters.count == 0{
//                return
//            }
           
            self.tblViewController.reloadData()
            self.txtChatMsg.text = ""
            self.lblplaceholder.isHidden = false
            self.view.endEditing(true)
            self.viewBotteam.constant = 0
            self.viewheightConstrain.constant = 50
            self.viewHeight.constant = 50
        }
       
    }
    
}

