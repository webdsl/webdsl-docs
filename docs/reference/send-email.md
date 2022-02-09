# Send Email

This page describes how to create an email template and send email from your application. Make sure the email settings are configured in application.ini, see [[selectpage(Manual/ApplicationConfiguration)|Application Configuration]]. If you are interested in storing email addresses in an entity, have a look at the [Email](https://webdsl.org/selectpage/Manual/Types#Email) type page.

Defining an email template:

    define email testemail(us : User) {
      to(us.mail)
      from("webdslorg@gmail.com")
      subject("Test Email")
  
      par{ "Dear " output(us.name) ", " }
      par{
        "Look at your profile page: "
        navigate(user(us)){"go"}
        //navigate will become an absolute link in the email
      }
    }

Sending email:

      email testemail(someuser);

The actual sending happens asynchronously, if there are issues while the application is trying to send an email, it will retry that email after 3 hours. If necessary, you can inspect and influence this email queue through the `QueuedEmail` entity:

    entity QueuedEmail {
      body :: String (length=1000000) 
        //Note: default length for string is currently 255
      to :: String (length=1000000)
      cc :: String (length=1000000)
      bcc :: String (length=1000000)
      replyTo :: String (length=1000000)
      from :: String (length=1000000)
      subject :: String (length=1000000)
      lastTry :: DateTime 
    }
