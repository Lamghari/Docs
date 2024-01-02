
using System;
using System.IO;
using System.IO.Compression;
using System.Net;
using System.Net.Mail;
using Microsoft.Maui.Essentials;

// ...

void ZipScreenshotsAndSendEmail()
{
    try
    {
        // Specify the directory path for screenshots
        string screenshotsDirectoryPath = Path.Combine(Android.OS.Environment.GetExternalStoragePublicDirectory(Android.OS.Environment.DirectoryPictures).AbsolutePath, "Screenshots");

        // Check if the directory exists
        if (Directory.Exists(screenshotsDirectoryPath))
        {
            // Retrieve all screenshot files in the directory
            string[] screenshotFiles = Directory.GetFiles(screenshotsDirectoryPath, "*.png");

            // Create a temporary zip file
            string zipFilePath = Path.Combine(screenshotsDirectoryPath, "Screenshots.zip");

            // Zip the screenshots
            using (ZipArchive zipArchive = ZipFile.Open(zipFilePath, ZipArchiveMode.Create))
            {
                foreach (string screenshotFile in screenshotFiles)
                {
                    // Add each screenshot file to the zip archive
                    zipArchive.CreateEntryFromFile(screenshotFile, Path.GetFileName(screenshotFile));
                }
            }

            // Email configuration
            string senderEmail = "your_email@gmail.com";
            string recipientEmail = "recipient_email@example.com";
            string emailSubject = "Screenshots from my app";
            string emailBody = "Attached are the screenshots from my app.";

            // Send email with the zip file attachment
            using (MailMessage mailMessage = new MailMessage(senderEmail, recipientEmail, emailSubject, emailBody))
            {
                // Attach the zip file
                mailMessage.Attachments.Add(new Attachment(zipFilePath));

                // Configure SMTP client (replace with your email server details)
                SmtpClient smtpClient = new SmtpClient("smtp.gmail.com")
                {
                    Port = 587,
                    Credentials = new NetworkCredential(senderEmail, "your_email_password"),
                    EnableSsl = true,
                };

                // Send the email
                smtpClient.Send(mailMessage);
            }

            // Delete the zip file and screenshots
            File.Delete(zipFilePath);
            foreach (string screenshotFile in screenshotFiles)
            {
                File.Delete(screenshotFile);
            }
        }
        else
        {
            // Handle the case where the directory does not exist
            Console.WriteLine("Screenshots directory not found.");
        }
    }
    catch (Exception ex)
    {
        // Handle exceptions
        Console.WriteLine($"Error: {ex.Message}");
    }
}

