title: Automated webpage screenshots using Selenium
date: 2015-11-12 19:52:12
tags: #Selenium #QA #screenshots #automated
---
Before I became a developer, I was helping with QA for a Java based web application. Thusly, I am familiar with the pain of running through a detailed and laborious regression test plan and then finding out about a bug related to how a page is being displayed after the code has already been deployed to Production. In retrospect, something that would have helped is a screenshot of each web page on the application as they are displayed in each of the supported browsers. If I had these screenshots, I could have more easily dedicated precious time specifically to reviewing each page to look for display issues across each browser. Or better yet, I could have ran a diff between an expected image and the image that was actually obtained during regression testing.

As it turns out there are methods available to make this wish a reality. If you are familiar with Selenium Web Driver, their solution is probably the least complex. Selenium has an interface called TakesScreenshot that contains a getScreenshotAs method which obtains a full page screenshot of whatever URL you send the web driver to. Using this method, I can let Selenium run through each URL and save an image to a local folder for me to review.

The important bit of code is as follows:
``` bash
FirefoxDriver driver = new FirefoxDriver();
driver.get("yoururl");
File srcFile = ((TakesScreenshot)driver).getScreenshotAs(OutputType.FILE);			
FileUtils.copyFile(srcFile, new File("c:\\tmp\\img_filename.png"));		
```

In my case, I have one Selenium test run through a handful of URLs to retrieve the images. After the test is completed, I simply open one of the images in an image viewer (Windows Photo Viewer in my case) and use the left and right keys to view each page in succession. This makes it a lot easier for me to pick out discrepancies, since my focus is entirely dedicated to finding display issues rather than to running through a complex list of regression test instructions. My Firefox screenshot test file is as follows. You can obtain screenshots for each supported browser by replacing the FirefoxDriver with that of a different browser.
``` bash
package com.fauxshopselenium.firefox;

import java.io.File;
import java.util.concurrent.TimeUnit;

import org.apache.commons.io.FileUtils;
import org.junit.AfterClass;
import org.junit.BeforeClass;
import org.junit.Test;
import org.openqa.selenium.OutputType;
import org.openqa.selenium.TakesScreenshot;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.firefox.FirefoxDriver;
import org.openqa.selenium.support.ui.WebDriverWait;

public class ScreenshotTest {	
	private static FirefoxDriver driver;
	WebElement element;
	WebDriverWait wait = new WebDriverWait(driver, 40);
	
	@BeforeClass
	public static void openBrowser(){
		driver = new FirefoxDriver();
		driver.manage().timeouts().implicitlyWait(10,  TimeUnit.SECONDS);
	}
	
	@Test
	public void retrieveScreenshots(){		
		System.out.println("Starting test " + new Object(){}.getClass().getEnclosingMethod().getName());		
		try {			
			driver.get("localhost:8080/fauxshop/about");
			File scrFile = ((TakesScreenshot)driver).getScreenshotAs(OutputType.FILE);			
			FileUtils.copyFile(scrFile, new File("c:\\tmp\\about_firefox.png"));			
			driver.get("localhost:8080/fauxshop/account");
			File scrFile2 = ((TakesScreenshot)driver).getScreenshotAs(OutputType.FILE);			
			FileUtils.copyFile(scrFile2, new File("c:\\tmp\\account_firefox.png"));			
			driver.get("localhost:8080/fauxshop/cart");
			File scrFile3 = ((TakesScreenshot)driver).getScreenshotAs(OutputType.FILE);			
			FileUtils.copyFile(scrFile3, new File("c:\\tmp\\cart_firefox.png"));
			driver.get("localhost:8080/fauxshop/categories/101");
			File scrFile4 = ((TakesScreenshot)driver).getScreenshotAs(OutputType.FILE);						
			FileUtils.copyFile(scrFile4, new File("c:\\tmp\\cart_firefox.png"));
			driver.get("localhost:8080/fauxshop/contact");
			File scrFile5 = ((TakesScreenshot)driver).getScreenshotAs(OutputType.FILE);						
			FileUtils.copyFile(scrFile5, new File("c:\\tmp\\contact_firefox.png"));
			driver.get("localhost:8080/fauxshop/index");
			File scrFile6 = ((TakesScreenshot)driver).getScreenshotAs(OutputType.FILE);						
			FileUtils.copyFile(scrFile6, new File("c:\\tmp\\index_firefox.png"));
			driver.get("localhost:8080/fauxshop/login");
			File scrFile7 = ((TakesScreenshot)driver).getScreenshotAs(OutputType.FILE);						
			FileUtils.copyFile(scrFile7, new File("c:\\tmp\\login_firefox.png"));
			driver.get("localhost:8080/fauxshop/product_detail/-114?color=Red");
			File scrFile8 = ((TakesScreenshot)driver).getScreenshotAs(OutputType.FILE);						
			FileUtils.copyFile(scrFile8, new File("c:\\tmp\\product_detail_firefox.png"));			
		} catch (Exception e) {
		}				
		System.out.println("Ending test " + new Object(){}.getClass().getEnclosingMethod().getName());
	}
	
	@AfterClass
	public static void closeBrowser(){
		driver.quit();
	}	
```