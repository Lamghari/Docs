
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






using System;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Maui.Controls;
using Xamarin.Essentials;

namespace YourNamespace
{
    public partial class MainPage : ContentPage
    {
        private Timer screenshotDetectionTimer;

        public MainPage()
        {
            InitializeComponent();

            // Initialize and start the timer to periodically check for screenshots
            screenshotDetectionTimer = new Timer(CheckForScreenshots, null, TimeSpan.Zero, TimeSpan.FromSeconds(5));
        }

        private async void CheckForScreenshots(object state)
        {
            try
            {
                // Query the MediaStore for new images
                var recentImages = await MediaPicker.PickMultiplePhotosAsync(new PickMediaOptions
                {
                    PresentationSourceBounds = System.Drawing.Rectangle.Empty,
                    ModalPresentationStyle = MediaPickerModalPresentationStyle.OverFullScreen
                });

                if (recentImages != null && recentImages.Count > 0)
                {
                    // Check if the latest image matches the pattern used for screenshots
                    var latestImage = recentImages[recentImages.Count - 1];
                    if (IsScreenshot(latestImage))
                    {
                        // Display a message or perform any action when a screenshot is detected
                        Console.WriteLine("Screenshot taken!");
                    }
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine($"Exception: {ex.Message}");
            }
        }

        private bool IsScreenshot(FileResult fileResult)
        {
            // You may need to customize this logic based on how your device names screenshot files.
            return fileResult.FullPath.ToLowerInvariant().Contains("screenshot");
        }
    }
}
