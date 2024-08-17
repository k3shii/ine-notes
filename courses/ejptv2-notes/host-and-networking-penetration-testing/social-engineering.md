# Social Engineering

> **⚡ Prerequisites**
>
> * Basic Network and Cybersecurity Concepts
>
> **📕 Learning Objectives**
>
> * Perform **phishing** and **watering hole** attacks
> * Describe **Social Engineering** fundamentals
> * Identify techniques and real-life scenarios

🗒️ **Social engineering** is a type of cyber attack that exploits human psychology to gain access to sensitive information or systems. Instead of using technical exploits to hack into a system, social engineers manipulate people into giving up confidential information (_gather information_) or performing actions that compromise security (_gain access_).

The success of social engineering attacks relies on the attacker's ability to **create a convincing scenario** that triggers an _emotional response, such as curiosity, fear, or urgency, in the victim_.

Social engineering through physical security is an effective tactic to gain **physical access** to a restricted building or office area.

Here are some examples of manipulating attacks:

* Phishing, Vishing, Smishing
* Impersonation
* [Pretexting](https://github.com/L4bF0x/PhishingPretexts)
* Emotional pull, Urgency
* Quid pro quo, Blackmail
* Watering hole

> ❗ **It's important to stay vigilant against phishing attacks and be wary of any unsolicited messages asking for personal or sensitive information.**

## Phishing <a href="#phishing" id="phishing"></a>

🗒️ **Phishing** is the most common form of social engineering attack. Attackers send fraudulent emails that appear to be from a reputable source, such as a bank or social media site, to trick the victim into revealing their login credentials, credit card numbers, or other sensitive information.

* **`e.g.`** Emails with malicious links and/or attachments

Common types of phishing attacks are:

* `Spear Phishing` - _targeted attack where the attacker researches and personalizes the message to increase the likelihood of success_
* `Whaling` - _targets high-profile individuals_, such as executives or celebrities, who have access to sensitive information or financial resources
* `Smishing` - phishing attack that _uses SMS text messages to trick the victim into revealing sensitive information or downloading malware_
* `Vishing` - phishing attack that uses _voice communication, such as a phone call, to trick the victim into revealing sensitive information_
* `Baiting` - _leaving a physical device, such as a USB drive, in a public place for the victim to pick it up and plug it into their computer, unintentionally downloading malware, keylogger, backdoor_

Getting people's trust is a big part of a phishing attack and can be done through different approaches like redirecting web traffic maliciously, targeting a trusted site and use it against the victim (**watering hole**), compromise business email (**BEC**), spoofing and impersonation.

> 📌 [FBI IC3 - 2022 Internet Crime Report](https://www.ic3.gov/Media/PDF/AnnualReport/2022\_IC3Report.pdf)
>
> ![IC3 Report - 2022](https://blog.syselement.com/\~gitbook/image?url=https%3A%2F%2F1996978447-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FlhjuckuLbvBn36EoFL7P%252Fuploads%252Fgit-blob-91ed6c578deaa383bdcc8a7559e6d4221df4fbfa%252Fimage-20230430173841740.png%3Falt%3Dmedia\&width=300\&dpr=4\&quality=100\&sign=fc5dd412\&sv=1)
>
> ![IC3 Report - 2022](https://blog.syselement.com/\~gitbook/image?url=https%3A%2F%2F1996978447-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FlhjuckuLbvBn36EoFL7P%252Fuploads%252Fgit-blob-57273369be3918a5f58ae97927cc4425d4ebdd2d%252Fimage-20230430173938810.png%3Falt%3Dmedia\&width=300\&dpr=4\&quality=100\&sign=d997944d\&sv=1)
>
> ![IC3 Report - 2022](https://blog.syselement.com/\~gitbook/image?url=https%3A%2F%2F1996978447-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FlhjuckuLbvBn36EoFL7P%252Fuploads%252Fgit-blob-e302f5aeba1d122ee54eae7298a36d6e71b11eb8%252Fimage-20230430174159761.png%3Falt%3Dmedia\&width=300\&dpr=4\&quality=100\&sign=83cce61f\&sv=1)
>
> 📌 [Trendmicro Security 101: Business Email Compromise (BEC) Schemes](https://www.trendmicro.com/vinfo/fr/security/news/cybercrime-and-digital-threats/business-email-compromise-bec-schemes)
>
> 📌 [CEO Fraud Attacks - KnowBe4](https://www.knowbe4.com/ceo-fraud)

It is essential for organizations to take social engineering attacks seriously and implement comprehensive cybersecurity measures that include **employee training**, **security awareness programs**, and regular **security audits and controls** to identify and address vulnerabilities in their systems and processes. _By staying vigilant and educating employees on how to identify and respond to social engineering attacks, organizations can significantly **reduce the risk** of a successful cyber attack._

## Case Studies <a href="#case-studies" id="case-studies"></a>

* [Google and Facebook fake invoicing](https://www.trendmicro.com/vinfo/fr/security/news/cybercrime-and-digital-threats/google-and-facebook-fraudster-pleads-guilty-to-100-million-scam)
* [FACC CEO fraud](https://www.trendmicro.com/vinfo/pl/security/news/cybercrime-and-digital-threats/austrian-aeronautics-company-loses-42m-to-bec-scam)
* [Robinhood Vishing](https://www.bleepingcomputer.com/news/security/robinhood-discloses-data-breach-impacting-7-million-customers/)
* [Fake Excel file - Emotet](https://unit42.paloaltonetworks.com/new-emotet-infection-method/)
* [HTML Table Windows Logo](https://www.microsoft.com/en-us/security/blog/2021/08/18/trend-spotting-email-techniques-how-modern-phishing-emails-hide-in-plain-sight/)
* [FIN7 USB in Mail](https://www.bleepingcomputer.com/news/security/fbi-hackers-use-badusb-to-target-defense-firms-with-ransomware/)

## Penetration Testing <a href="#penetration-testing" id="penetration-testing"></a>

The purpose of **social engineering in a penetration test** is to _identify weaknesses in an organization's security systems and processes and to raise awareness among employees about the risks of social engineering attacks._

> 📌 [Social Engineering Penetration Testing: Attacks, Methods, & Steps - Purplesec.us](https://purplesec.us/social-engineering-penetration-testing/)
>
> 📌 [Social Engineering Community ad DEFCON](https://www.se.community/)

* **Information Gathering** - _OSINT, dumpster diving, shoulder surfing, pretexting_
* **External Access** to internal I.T. infrastructure - _malware, credentials attacks, reverse shells_
* **Physical Access** - _attempt to gain physical access to the organization's facilities by impersonating employees or contractors, employees badges scan, malicious USBs, work people impersonation_

## [🔬 Gophish](https://getgophish.com/) <a href="#gophish" id="gophish"></a>

[**`Gophish`**](https://github.com/gophish/gophish) - _a powerful, easy-to-use, open-source phishing toolkit meant to help pentesters and businesses conduct **real-world phishing simulations**._

> ❗ **Phishing campaigns should only be conducted for legitimate security testing purposes**, and not for malicious intent. Additionally, it's crucial to obtain **permission** and **informed consent** from the costumer involved in the campaign.

```bash
# Linux Install
cd /opt/

# Get the latest version link from https://github.com/gophish/gophish/releases/
sudo wget https://github.com/gophish/gophish/releases/download/v0.12.1/gophish-v0.12.1-linux-64bit.zip

sudo unzip -d gophish gophish-v0.12.1-linux-64bit.zip

sudo chmod +x gophish/gophish

cd /opt/gophish && sudo ./gophish
```

* To create a demo instance with a fake campaign, you can use `docker` and this command, made available by the Gophish maintainer [Jordan Wright](https://github.com/jordan-wright)
  * [Creating the Gophish Demo: Part One](https://getgophish.com/blog/post/2019-01-04-creating-the-gophish-demo-part-one/)
  * [Creating the Gophish Demo: Part Two](https://getgophish.com/blog/post/2019-01-11-creating-the-gophish-demo-part-two/)

```bash
docker run -ti -p 3333:3333 --rm gophish/demo
```

The basics steps to create a phishing campaign with Gophish are:

* Create a `Sending Profile`

<figure><img src="https://blog.syselement.com/~gitbook/image?url=https%3A%2F%2F1996978447-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FlhjuckuLbvBn36EoFL7P%252Fuploads%252Fgit-blob-0434f06b839a0ab150b0dbdf085e398284bbc9fd%252Fimage-20230430215816312.png%3Falt%3Dmedia&#x26;width=768&#x26;dpr=4&#x26;quality=100&#x26;sign=1000116e&#x26;sv=1" alt=""><figcaption></figcaption></figure>

* Set up a new `Landing Page` - the landing page is the website or form that the victim will be directed to after clicking on the phishing link

<figure><img src="https://blog.syselement.com/~gitbook/image?url=https%3A%2F%2F1996978447-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FlhjuckuLbvBn36EoFL7P%252Fuploads%252Fgit-blob-09daa76902dc9987712f9bc030e553aed33164f1%252Fimage-20230430220314084.png%3Falt%3Dmedia&#x26;width=768&#x26;dpr=4&#x26;quality=100&#x26;sign=27c46f28&#x26;sv=1" alt=""><figcaption></figcaption></figure>

* Customize the `Email Template`- Gophish provides a set of pre-built email templates, but it can also be create by using the HTML editor or importing an existing template. Customize the email to make it look convincing and relevant to the target audience

<figure><img src="https://blog.syselement.com/~gitbook/image?url=https%3A%2F%2F1996978447-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FlhjuckuLbvBn36EoFL7P%252Fuploads%252Fgit-blob-f31d5d89fef7ec0eca580ecf7146f9daefe2b60f%252Fimage-20230430220437682.png%3Falt%3Dmedia&#x26;width=768&#x26;dpr=4&#x26;quality=100&#x26;sign=3d34cc4e&#x26;sv=1" alt=""><figcaption></figcaption></figure>

* Create `Users & Groups`- specify targeted users' emails or bulk import users

<figure><img src="https://blog.syselement.com/~gitbook/image?url=https%3A%2F%2F1996978447-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FlhjuckuLbvBn36EoFL7P%252Fuploads%252Fgit-blob-cabf17a73c783786ba8c7d6bc571e7ce5f398f8f%252Fimage-20230430220810824.png%3Falt%3Dmedia&#x26;width=768&#x26;dpr=4&#x26;quality=100&#x26;sign=b3d7228c&#x26;sv=1" alt=""><figcaption></figcaption></figure>

* Create a `Campaign` - give the campaign a name, select the type of phishing campaign to create (such as credential harvesting or malicious attachment), and specify the targets and email template

<figure><img src="https://blog.syselement.com/~gitbook/image?url=https%3A%2F%2F1996978447-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FlhjuckuLbvBn36EoFL7P%252Fuploads%252Fgit-blob-b2f104f99656c0ce85dff118f7f8eb635af69484%252Fimage-20230430220159433.png%3Falt%3Dmedia&#x26;width=768&#x26;dpr=4&#x26;quality=100&#x26;sign=283a6223&#x26;sv=1" alt=""><figcaption></figcaption></figure>

* `Launch the campaign` - Gophish will automatically send the phishing emails to the target list and track who clicked on the link and submitted data
* Analyze the results provided by Gophish as detailed metrics on email opens, link clicks and successful credential submissions.

### Solution

**Step 1:** Open the lab link to access the Windows GUI instance

This lab environment will provide you with access to a pre-configured Windows system that has all the required tools installed.

**Step 2:** Starting the Gophish server

To begin with, you will need to startup the Gophish server, this can be done by double clicking on the **gophish.exe** shortcut on the desktop of the Windows system you have been provided access to as shown in the following screenshot.

<figure><img src="https://assets.ine.com/content/ptp/AlexisAhmed/VOD-4379/LAB-3874/1.png" alt=""><figcaption></figcaption></figure>

As shown in the following screenshot, once the Gophish server is started up you will be provided with a command prompt session that will display the current working state of the Gophish server in addition to the local port it is listening on.

<figure><img src="https://assets.ine.com/content/ptp/AlexisAhmed/VOD-4379/LAB-3874/2.png" alt=""><figcaption></figcaption></figure>

In this case, the Gophish server is listening on port **https://127.0.0.1:3333**.

You can access the Gophish admin panel by navigating to the aforementioned address in a web browser like Firefox.

<figure><img src="https://assets.ine.com/content/ptp/AlexisAhmed/VOD-4379/LAB-3874/3.png" alt=""><figcaption></figcaption></figure>

The admin panel does not have a valid SSL certificate, as a result, you will need to click on the advanced button and click on "accept the risk and continue" as shown in the following screenshot.

<figure><img src="https://assets.ine.com/content/ptp/AlexisAhmed/VOD-4379/LAB-3874/4.png" alt=""><figcaption></figcaption></figure>

You will now be prompted with the Gophish admin panel authentication form, you can login with the following credentials: **admin:phishingpasswd**.

<figure><img src="https://assets.ine.com/content/ptp/AlexisAhmed/VOD-4379/LAB-3874/5.png" alt=""><figcaption></figcaption></figure>

After logging in successfully, you will be redirected to the Gophish dashboard.

<figure><img src="https://assets.ine.com/content/ptp/AlexisAhmed/VOD-4379/LAB-3874/6.png" alt=""><figcaption></figcaption></figure>

**Step 3:** Setting up a Gophish sending profile

Now that we have started the Gophish server and logged in to the admin panel, we can begin the process of setting up a phishing email.

To begin with, we will need to setup a sending profile, this can be done by clicking on the **Sending Profiles** menu item on the sidebar of the dashboard as shown in the following screenshot.

<figure><img src="https://assets.ine.com/content/ptp/AlexisAhmed/VOD-4379/LAB-3874/7.png" alt=""><figcaption></figcaption></figure>

We can now setup a new sending profile by clicking on the **New Profile** button.

<figure><img src="https://assets.ine.com/content/ptp/AlexisAhmed/VOD-4379/LAB-3874/8.png" alt=""><figcaption></figcaption></figure>

This will bring up the sending profile customization prompt where you can specify different criteria based on your requirements, in this case, we will use the following configuration for the sending profile:

1. Name: red
2. From: info [**support@demo.ine.local**](mailto:support@demo.ine.local)
3. Host: localhost:25
4. Username: red@demo.ine.local
5. Password: penetrationtesting

<figure><img src="https://assets.ine.com/content/ptp/AlexisAhmed/VOD-4379/LAB-3874/9.png" alt=""><figcaption></figcaption></figure>

After configuring the sending profile accordingly, we can test the configuration by clicking on the **Send Test Email** button as shown in the following screenshot.

<figure><img src="https://assets.ine.com/content/ptp/AlexisAhmed/VOD-4379/LAB-3874/10.png" alt=""><figcaption></figcaption></figure>

This will prompt you to specify the details of the recipient, in this case we will be sending the test email to **victim@demo.ine.local** whose mailbox has already been configured in Thunderbird.

<figure><img src="https://assets.ine.com/content/ptp/AlexisAhmed/VOD-4379/LAB-3874/11.png" alt=""><figcaption></figcaption></figure>

To verify that the test email was sent successfully, we can open up the Thunderbird email client installed and configured on the Windows system. As shown in the following screenshot, the test email was sent and received successfully thus confirming that the sending profile is functional.

<figure><img src="https://assets.ine.com/content/ptp/AlexisAhmed/VOD-4379/LAB-3874/12.png" alt=""><figcaption></figcaption></figure>

We can now save the sending profile.

<figure><img src="https://assets.ine.com/content/ptp/AlexisAhmed/VOD-4379/LAB-3874/13.png" alt=""><figcaption></figcaption></figure>

**Step 4:** Setting up a Gophish landing page

In order for us to setup a successful phishing campaign, we will need to setup a landing page where target users will be directed to for credential harvesting.

To begin with, we can click on the **Landing Pages** menu item on the side menu of the dashboard as shown in the following screenshot.

<figure><img src="https://assets.ine.com/content/ptp/AlexisAhmed/VOD-4379/LAB-3874/14.png" alt=""><figcaption></figcaption></figure>

We can then create a new landing page by clicking on the **New Page** button as highlighted in the preceding screenshot.

This will bring up the landing page customization prompt, where we can specify the site we would like to emulate or clone for the purpose of the phishing campaign.

In this case, we will be using a custom landing page being hosted on the **localhost:8080**.

As shown in the following screenshot, we can specify the landing page name and click on the **Import Site** button to import the pre-existing landing page.

<figure><img src="https://assets.ine.com/content/ptp/AlexisAhmed/VOD-4379/LAB-3874/15.png" alt=""><figcaption></figcaption></figure>

This will prompt you to specify the URL of the site you would like to import, as shown in the following screenshot, we will specify the URL **http://localhost:8080**, after which, you can click on import.

<figure><img src="https://assets.ine.com/content/ptp/AlexisAhmed/VOD-4379/LAB-3874/16.png" alt=""><figcaption></figcaption></figure>

After importing the site, you will be able to modify the appearance of the site and the data that is collected.

In this case, we will be capturing all submitted data and will be redirecting users to the landing page hosted on **localhost:8080**.

<figure><img src="https://assets.ine.com/content/ptp/AlexisAhmed/VOD-4379/LAB-3874/17.png" alt=""><figcaption></figcaption></figure>

After configuring the landing page, we can save the page by clicking on the **Save Page** button as shown in the preceding screenshot.

**Step 5:** Setting up a Gophish email template

Now that we have setup the sending profile and landing page, we can move on to the next step which involves setting up the email template for this phishing campaign.

To begin with, we can create a new email template by clicking on the **Email Templates** menu item on the sidebar. You will then need to click on the **New Template** button as shown in the following screenshot.

<figure><img src="https://assets.ine.com/content/ptp/AlexisAhmed/VOD-4379/LAB-3874/18.png" alt=""><figcaption></figcaption></figure>

An email template called **Password Reset Email.txt** has been provided to you in the lab environment on the Desktop of the Windows system as shown in the following screenshot.

<figure><img src="https://assets.ine.com/content/ptp/AlexisAhmed/VOD-4379/LAB-3874/19.png" alt=""><figcaption></figcaption></figure>

You will need to copy the content of the email template as we will be using it for our phishing campaign.

We can now setup the new Gophish email template. As shown in the following screenshot, paste the content of the email template you copied previously in the body section of the email template.

<figure><img src="https://assets.ine.com/content/ptp/AlexisAhmed/VOD-4379/LAB-3874/20.png" alt=""><figcaption></figcaption></figure>

We can now save the email template by clicking on the **Save Template** button.

<figure><img src="https://assets.ine.com/content/ptp/AlexisAhmed/VOD-4379/LAB-3874/21.png" alt=""><figcaption></figcaption></figure>

**Step 6:** Setting up a users & groups

The next step will involve selecting or specifying the target users for our phishing campaign.

The lab environment has provided you with a file called **targets.csv** on the Desktop of the Windows system as shown in the following screenshot.

<figure><img src="https://assets.ine.com/content/ptp/AlexisAhmed/VOD-4379/LAB-3874/22.png" alt=""><figcaption></figcaption></figure>

We can import these users by clicking on the **Users & Groups** menu item on the sidebar of the dashboard as shown in the following screenshot.

<figure><img src="https://assets.ine.com/content/ptp/AlexisAhmed/VOD-4379/LAB-3874/23.png" alt=""><figcaption></figcaption></figure>

You will then need to click on the **New Group** button as shown in the preceding screenshot.

This will bring up the users & groups menu, where we can bulk import our users by selecting the **targets.csv** file stored on the Desktop.

<figure><img src="https://assets.ine.com/content/ptp/AlexisAhmed/VOD-4379/LAB-3874/24.png" alt=""><figcaption></figcaption></figure>

After importing the users, we can save the changes.

**Step 7:** setting up a Gophish campaign

The final step will involve setting up our phishing campaign.

We can create a new campaign by clicking on the **Campaigns** menu item on the sidebar of the dashboard as shown in the following screenshot.

<figure><img src="https://assets.ine.com/content/ptp/AlexisAhmed/VOD-4379/LAB-3874/25.png" alt=""><figcaption></figcaption></figure>

Creating a new campaign will bring up the campaign configuration menu where you will need to specify the email template, landing page and sending profile you created in the previous steps.

You will also need to specify the Groups/users you imported.

<figure><img src="https://assets.ine.com/content/ptp/AlexisAhmed/VOD-4379/LAB-3874/26.png" alt=""><figcaption></figcaption></figure>

We can now launch the campaign by clicking on the launch campaign button highlighted in the preceding screenshot.

Launching the campaign will redirect you to the campaign dashboard that will provide you with a summary of statistics pertinent to the phishing campaign.

<figure><img src="https://assets.ine.com/content/ptp/AlexisAhmed/VOD-4379/LAB-3874/27.png" alt=""><figcaption></figcaption></figure>

We can check the emails sent to the target email by opening up the Thunderbird email client.

<figure><img src="https://assets.ine.com/content/ptp/AlexisAhmed/VOD-4379/LAB-3874/28.png" alt=""><figcaption></figcaption></figure>

As shown in the preceding screenshot, the phishing campaign was sent successfully.

We can test the phishing page by clicking on the **Reset Password** link sent in the phishing email.

After clicking on the phishing link, we can check the Gophish dashboard to confirm that the phishing link was clicked and opened.
