Développeur C# .NET avec plus de 6 années d'expérience, ma passion pour les nouvelles technologies a guidé mes différentes expériences professionnelles. 
En tant que développeur C# .NET, j'ai contribué à la réalisation de nombreux projets dans divers domaines. Familiarisé avec le travail en équipe et la méthodologie agile, je suis prêt à mettre à profit mes connaissances pour garantir le succès de votre projet.




using System.IO;
using Microsoft.Maui.Essentials;

// ...

void GetScreenshotsFromMediaDirectory()
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

            // Process each screenshot file
            foreach (string screenshotFile in screenshotFiles)
            {
                // Perform actions on each screenshot file, such as displaying or processing
                Console.WriteLine($"Found screenshot: {screenshotFile}");
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




