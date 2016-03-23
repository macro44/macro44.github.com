---
layout: post
title: "字符串过滤与限制和输入过滤与限制"
date: 2016-03-23 15:51:06 +0800
comments: true
categories: 字符串
---
> 在输入的时候经常会遇到要限制输入字符串长度和特殊字符的情况，这里就总结一下

<!--more-->

##字符串操作

以下所有字符串的操作都是基于一个NSString的拓展

###限制字符串的长度

	testString.length 表示字符串的长度 
	
###过滤字符串首尾的空格与换行
	
	- (NSString*)stringExceptSpaceAndNewLineAtStartAndEnd{
	    NSCharacterSet *set = [NSCharacterSet whitespaceAndNewlineCharacterSet];
	    NSString *dealedString = [self stringByTrimmingCharactersInSet:set];
	    return dealedString;
	}
	
###过滤字符串中所有的空格与换行

	- (NSString*)stringExceptAllSpaceAndNewLine{
	    NSString* stringExcaptSpace = [self stringByReplacingOccurrencesOfString:@" " withString:@""];
	    NSString* stringExcaptTable = [stringExcaptSpace stringByReplacingOccurrencesOfString:@"\r" withString:@""];
	    NSString* stringExcaptNewLine = [stringExcaptTable stringByReplacingOccurrencesOfString:@"\n" withString:@""];
	    return stringExcaptNewLine;
	}
	
###过滤字符串中首尾特殊的字符
	
	- (NSString*)stringExceptSpecialCharacterInString:(NSString*)characterString{
	    NSCharacterSet *set = [NSCharacterSet characterSetWithCharactersInString:characterString];
	    NSString *dealedString  =[self stringByTrimmingCharactersInSet:set];
	    return  dealedString;
	}
	
<font color="red">注意:</font> 此方法只去除字符串首尾包含在characterString中的字符，且不能隔着其他的字符，例如,characterString 为"[]",而要过滤的字符串为"[123]",则过滤后的字符串为"123]",后面的括号不能过滤

##输入操作
以下的操作是基于UITextFieldDelegate中的[- (BOOL)textField:(UITextField *)textField shouldChangeCharactersInRange:(NSRange)range replacementString:(NSString *)string](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITextFieldDelegate_Protocol/)函数
###限制输入的长度 
	
	- (BOOL)textField:(UITextField *)textField shouldChangeCharactersInRange:(NSRange)range replacementString:(NSString *)string{
	    if (textField == self.textfield) {
	        if (textField.text.length-range.length + string.length > 20) {
	            return NO;
	        }else{
	            return YES;
	        }
	    }
	    return YES;
	}
	
###过滤输入的特殊字符 

	- (BOOL)textField:(UITextField *)textField shouldChangeCharactersInRange:(NSRange)range replacementString:(NSString *)string{
	    if (textField == self.textfield) {
	        if (string.length > 0) {
	            NSCharacterSet *set = [NSCharacterSet characterSetWithCharactersInString:@"[]"];
	            NSString* dealedString = [string stringByTrimmingCharactersInSet:set];
	            if (dealedString.length == 0) {
	                return NO;
	            }
	        }
	    }
	    return YES;
	}