javascript
import com.aventstack.extentreports.ExtentTest;
import com.aventstack.extentreports.Status;
import com.aventstack.extentreports.markuputils.ExtentColor;
import com.aventstack.extentreports.markuputils.MarkupHelper;
import com.qa.utils.*;
import io.appium.java_client.pagefactory.AndroidFindBy;
import org.openqa.selenium.By;
import org.openqa.selenium.WebElement;
import java.io.*;
import java.util.List;
import java.util.Properties;
import java.util.Scanner;
import org.testng.asserts.SoftAssert;
import javax.sound.sampled.LineUnavailableException;
import javax.sound.sampled.UnsupportedAudioFileException;
public class LoginPage extends BasePage {
	TestUtils utils = new TestUtils();
	BasePage basePage;
	Properties props = new PropertyManager().getProps();
	public static String outputFile = "C:\\Users\\Amitchabhor\\Downloads\\audio.mp3";
	static String Query;
	String activityBaseValue = null;
	String activityResultValue = null;
	int activityCount = 1;
	SoftAssert softAssert = new SoftAssert();
	String FILEPATH = ".\\src\\test\\resources\\Login.txt";
	@AndroidFindBy(xpath = "//android.view.View[@content-desc='+1']")
	private WebElement countryCode;
	@AndroidFindBy(xpath = "//android.widget.ImageView[@content-desc='India']")
	private WebElement India;
	@AndroidFindBy(xpath = "//android.widget.EditText")
	private WebElement textBox;
	@AndroidFindBy(xpath = "//android.view.View[@content-desc='Next']")
	private WebElement nextBtn;
	@AndroidFindBy(xpath = "//android.view.View[@content-desc='Login']")
	private WebElement loginBtn;
	@AndroidFindBy(xpath = "//android.widget.Button")
	private WebElement micBtn;
	@AndroidFindBy(xpath = "(//*[@class='android.widget.ImageView'][2])[2]")
	private WebElement sheelaIcon;
	@AndroidFindBy(xpath = "//*[@class='android.widget.Button']")
	private WebElement commonBtn;
	@AndroidFindBy(xpath = "//*[@content-desc='Profile']")
	private WebElement profile;
	@AndroidFindBy(xpath = "//*[@class='android.view.View'][contains(@content-desc,'DA')]")
	private WebElement account;
	@AndroidFindBy(xpath = "//*[@class='android.view.View'][contains(@content-desc,'DA')]/android.view.View")
	private List<WebElement> requestText;
	@AndroidFindBy(xpath = "//*[@class='android.widget.FrameLayout']//..//android.view.View[1]//..//android.view.View[1]/android.view.View[1]/android.view.View[1]/android.view.View[1]/android.view.View[1]")
	private WebElement homeButton;
	@AndroidFindBy(xpath = "//android.widget.Button[@content-desc='Yes']")
	private WebElement exitConversation;
	@AndroidFindBy(xpath = "//android.view.View[@content-desc='Exit']")
	private WebElement exitBtn;
	public LoginPage() throws IOException {
	}
	public void loginApp() throws InterruptedException, IOException {
		Reporter.initializeReport("Test");
		activityTest = Reporter.createTestCases(" UI Test cases");
		enterMobileNumber(props.getProperty("mobileNumber"));
		clickOnNextBtn();
		Thread.sleep(2000);
		enterPassword(props.getProperty("password"));
		clickOnLoginBtn();
		Thread.sleep(4000);
	}
	public LoginPage enterMobileNumber(String mobile) {
		try {
			click(textBox);
			clear(textBox);
			sendKeys(textBox, mobile, "Mobile No. is " + mobile);
			return this;
		} catch (Exception e) {
			throw new RuntimeException(e);
		}
	}
	public LoginPage clickOnNextBtn() {
		try {
			Thread.sleep(2000);
			click(nextBtn, "press next button");
			return this;
		} catch (Exception e) {
			throw new RuntimeException(e);
		}
	}
	public LoginPage enterPassword(String password) throws InterruptedException {
		try {
			click(textBox);
			clear(textBox);
			sendKeys(textBox, password, "Password is " + password);
			return this;
		} catch (Exception e) {
			throw new RuntimeException(e);
		}
	}
	public LoginPage clickOnLoginBtn() {
		try {
			click(loginBtn, "Press login button");
			return this;
		} catch (Exception e) {
			throw new RuntimeException(e);
		}
	}
	public LoginPage clickOnSheelaIcon(){
		try {
			Thread.sleep(6000);
			click(sheelaIcon);
			return this;
		} catch (Exception e) {
			throw new RuntimeException(e);
		}
	}
	public void executeSheelaQuery() throws UnsupportedAudioFileException, IOException, LineUnavailableException {
		try {
			basePage = new BasePage();
			File file = new File(FILEPATH);
			Scanner scanner = new Scanner(file);
			int count = 0;
			while (scanner.hasNextLine()) {
				count=0;
				Query = scanner.nextLine();
				sendSheelaQuery(Query);
				utils.log().info("Send Query to homepage- " +Query);
				count = validateRequest(Query, count);
				utils.log().info("Validate the request");
				count++;
				utils.log().info("count " +count);
				System.out.println(count);
				if(count>=1){
					if(waitForElementVisible(exitBtn)){
						click(exitBtn);
						waitForElementEnabled(micBtn);
						elementClickable(homeButton);
					}else{
						waitForElementVisibleAndClick(micBtn);
						Thread.sleep(1000);
						sendSheelaQuery("Exit the conversation");
						elementClickable(homeButton);
					}
					utils.log().info("Redirected to home screen");
					waitForElementVisibleAndClick(homeicon);
					utils.log().info("Clicked on Sheela icon");
				}
			}
			scanner.close();
		} catch (FileNotFoundException e) {
			System.err.println("File not found: " + e.getMessage());
		} catch (Exception e) {
			throw new RuntimeException(e);
		}
	}
	public int validateRequest(String inputQuery, int count){
		String requestedQueryElement = "";
		String Request = null;
		try {
			waitForElementEnabled(micBtn);
			requestedQueryElement = "(//*[@class='android.view.View'][contains(@content-desc,'DA')]/android.view.View)";
			WebElement query = driver.findElement(By.xpath(requestedQueryElement));
			if (query.isDisplayed()) {
				System.out.println("Request is successfully sent");
				Request = query.getAttribute("content-desc");
				softAssert.assertEquals(Request, inputQuery);
				if(inputQuery.equalsIgnoreCase(Request)){
					System.out.println("User utterance actual: " + Request);
					System.out.println("User utterance expected: " + inputQuery);
					activityTest.log(Status.PASS,
							MarkupHelper.createLabel(String.format("%s", "<p style=text-align:left;width:80%>" + "Query - " + inputQuery + "<br />" + "Expected&nbsp: "
											+ inputQuery.replaceAll(" ", "&nbsp") + "<br />" + "Actual&nbsp&nbsp&nbsp&nbsp&nbsp: " + Request.replaceAll(" ", "&nbsp") + "<br />" + "</p>"),
									ExtentColor.GREEN));
				}else{
					System.out.println("User utterance actual: " + Request);
					System.out.println("User utterance expected: " + inputQuery);
					activityTest.log(Status.FAIL,
							MarkupHelper.createLabel(String.format("%s)
