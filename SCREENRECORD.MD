# SCREENSHOT

AppRecordingManager manager = AppRecordingManager.GetDefault();
var status = manager.GetStatus();
if (status.CanRecord || status.CanRecordTimeSpan)
{
    var result = await manager.SaveScreenshotToFilesAsync(ApplicationData.Current.LocalFolder, "screnshot", AppRecordingSaveScreenshotOption.HdrContentVisible, manager.SupportedScreenshotMediaEncodingSubtypes);
    Debug.WriteLine(result.Succeeded);
    if (result.Succeeded)
    {
        foreach (var item in result.SavedScreenshotInfos)
        {
            Debug.WriteLine(item.File.DisplayName);
        }
    }
    else
    {
        Debug.WriteLine(result.ExtendedError.Message);
    }
}


# video record (allow in setting for game screen record)

            await AppCapture.SetAllowedAsync(true);

            AppRecordingManager manager = AppRecordingManager.GetDefault();
            var status = manager.GetStatus();
            //if (status.CanRecord || status.CanRecordTimeSpan)
            //{

                //var myVideo = await StorageLibrary.GetLibraryAsync(KnownLibraryId.Videos);
                //StorageFolder projectFolder = await myVideo.SaveFolder.CreateFolderAsync("DataFolder", CreationCollisionOption.OpenIfExists);
                StorageFolder projectFolder = ApplicationData.Current.LocalFolder;

                var record = await projectFolder.CreateFileAsync("audio_record.mp4", CreationCollisionOption.GenerateUniqueName);

                var result = await manager.StartRecordingToFileAsync(record);

                if (result.Succeeded)
                {
                    Debug.WriteLine(result.Succeeded);
                }
                else
                {
                    Debug.WriteLine(result.ExtendedError.Message);
                }
            //}


