//
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

