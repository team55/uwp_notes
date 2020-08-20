OLD MECHANIC
        
PLUSES AND MINUSES:
- SEPARATE THREAD, 
- NO BINDINGS TO SAME MODEL
- ALLOW USE PICKERS (screen record as example)




<!-- 

    https://docs.microsoft.com/ru-ru/windows/uwp/gaming/capture-game-audio-video-screenshots-and-metadata
    не совместим девайс и не может писать таймспан ??

    https://docs.microsoft.com/en-us/windows/uwp/design/layout/app-window
    не разрешено использовать пикер - возвращает ложь по дефолту
    стырый способ создания окна - разные потоки биндинга


 -->

        
        // ---------------------------------------------------
        #region RECORDER IMPLEMENTATION
        // ---------------------------------------------------

        public static CoreWindow recorderWindow = null;
        private static CoreDispatcher recorderDispatcher = null;
        
        public static async void OpenRecorderWindow()
        {
            // already open - skiping
            //if (screenShoterEnabled) return;
            //screenShoterEnabled = true;
            if (recorderWindow != null) return;


            await CoreApplication.CreateNewView().Dispatcher.RunAsync(CoreDispatcherPriority.Normal, async () =>
            {
                //var anchor = ApplicationView.GetForCurrentView().Id;

                CoreWindow newWindow = CoreWindow.GetForCurrentThread();
                recorderDispatcher = newWindow.Dispatcher; //already added ?
                recorderWindow = newWindow;


                var viewId = ApplicationView.GetApplicationViewIdForWindow(newWindow);
                var frame = new Frame();
                frame.SizeChanged += RecorderWindowSizeChanged;
                frame.Navigate(typeof(Recorder), newWindow); // or send class (control) ?
                
                Window.Current.Content = frame;

                // This is a change from 8.1: In order for the view to be displayed later it needs to be activated.
                Window.Current.Activate();

                var view = ApplicationView.GetForCurrentView();
                view.Title = "Recorder";
                //view.SetPreferredMinSize(new Size(300, 100));


                var viewShown = await ApplicationViewSwitcher.TryShowAsStandaloneAsync(viewId,
                    //ViewSizePreference.UseMinimum,
                    ViewSizePreference.Custom,
                    ApplicationView.GetForCurrentView().Id, //anchor
                    //anchor,
                    //ViewSizePreference.UseMinimum
                    ViewSizePreference.Custom
                    );



                //Enter CompactOverlay mode and set the form to 200 x 200 pixels
                var preferences = ViewModePreferences.CreateDefault(ApplicationViewMode.CompactOverlay);
                //preferences.CustomSize = new Size(354, 112);
                preferences.CustomSize = new Size(328, 150);
                //await ApplicationView.GetForCurrentView().TryEnterViewModeAsync(ApplicationViewMode.CompactOverlay, preferences);

                await view.TryEnterViewModeAsync(ApplicationViewMode.CompactOverlay, preferences);
            });

            // Be careful! This collection is bound to the current thread,
            // so make sure to update it only from this thread
            //((App)App.Current).SecondaryViews.Add(viewControl);
        }

        public static void CloseRecorderWindow()
        {
            Debug.WriteLine(String.Format("CLOSE Recorder: {0}", recorderWindow));

            if (recorderWindow != null) {


                // If called from the UI thread, then update immediately.
                // Otherwise, schedule a task on the UI thread to perform the update.
                if (recorderDispatcher.HasThreadAccess)
                {
                    recorderWindow.Close();
                    recorderWindow = null;
                    recorderDispatcher = null;
                }
                else
                {
                    var task = recorderDispatcher.RunAsync(CoreDispatcherPriority.Normal, () => {

                        if (recorderWindow != null) {
                            recorderWindow.Close();
                            recorderWindow = null;
                            recorderDispatcher = null;
                        }
                    });
                }
            }
        }

        private static void RecorderWindowSizeChanged(object sender, SizeChangedEventArgs e)
        {
            if (e.NewSize.Width != 328 || e.NewSize.Height != 184)
            {
                var view = ApplicationView.GetForCurrentView();
                if (view.ViewMode == ApplicationViewMode.CompactOverlay)
                {
                    view.TryResizeView(new Size(328, 184));
                }
            }
            //if (e.NewSize.Width != 300 || e.NewSize.Height != 200)
            //{
            //    var view = ApplicationView.GetForCurrentView();
            //    if (view.ViewMode == ApplicationViewMode.CompactOverlay)
            //    {
            //        view.TryResizeView(new Size(300, 200));
            //    }
            //}
        }

        #endregion