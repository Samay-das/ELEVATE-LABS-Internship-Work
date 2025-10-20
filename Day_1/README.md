# Day 1 : Create a Cloud Storage Bucket and Upload Files

## Task 1. Create a bucket

*Buckets* are the basic containers that hold your data in Cloud Storage.

To create a bucket:
1. In the Cloud console, go to **Navigation menu (Navigation menu)** > **Cloud Storage** > **Buckets**.
2. Click **+ Create**:
3. Enter your bucket information and click Continue to complete each step:
    - Name your bucket: Enter a unique name for your bucket.

    <hr>

    ## Bucket naming rules:
    * Do not include sensitive information in the bucket name, because the bucket namespace is global and publicly visible.

    * Bucket names must contain only lowercase letters, numbers, dashes (-), underscores (_), and dots (.). Names containing dots require verification.

    * Bucket names must start and end with a number or letter.

    * Bucket names must contain 3 to 63 characters. Names containing dots can contain up to 222 characters, but each dot-separated component can be no longer than 63 characters.

    * Bucket names cannot be represented as an IP address in dotted-decimal notation (for example, 192.168.5.4).

    * Bucket names cannot begin with the "goog" prefix. Bucket names cannot contain "google" or close misspellings of "google".*

    * Also, for DNS compliance and future compatibility, you should not use underscores (_) or have a period adjacent to another period or dash. For example, ".." or "-." or ".-" are not valid in DNS names.

    * Choose **Region** for **Location type** and <filled in at lab start> for Location.

    * Choose **Standard** for **default storage class**.

    * Choose **Uniform** for **Access control** and **uncheck** Enforce public access prevention on this bucket to turn it off.
    <hr>

4. Leave the rest of the fields as their default values and click **Create**.

## Task 2. Upload an object into the bucket

To upload the image above into your new bucket:


* In the Cloud Storage browser page, click the name of the bucket that you created.

* In the **Objects** tab, click **Upload > Upload files**.

* In the file dialog, go to the file that you downloaded and select it.

* Ensure the file is named **example.png**. If it is not, click the three dot icon for your file, select **Rename** from the dropdown, and rename the file to **example.png**.

After the upload completes, you should see the file name and information about the file, such as its size and type.

## Task 3. Share a bucket publicly

To allow public access to the bucket and create a publicly accessible URL for the image:

1. Click the **Permissions** tab above the list of files.

2. Ensure the view is set to **Principals**. Click **Grant Access** to view the Add **principals pane**.

3. In the **New principals** box, enter allUsers.

4. In the **Select a role** drop-down, **select Cloud Storage > Storage Object Viewer**.

5. Click **Save**.

6. In the **Are you sure you want to make this resource public?** window, click **Allow public access**.

<hr>

## To Verify the public *Bucket Storage*.

1. To verify, click the **Objects** tab to return to the list of objects. Your object's **Public access** column should read **Public to internet**.
2. Press the **Copy URL** button for your object and paste it into a separate tab to view your image.
<br>
The **Copy URL** button provides a shareable URL similar to the following:

    https://storage.googleapis.com/YOUR_BUCKET_NAME/emample.png

## Task 4. Create folders
1. In the **Objects** tab, click **Create folder**.

2. Enter **folder1** for **Name** and **click Create**.

You should see the folder in the bucket with an image of a folder icon to distinguish it from objects.

Create a subfolder and upload a file to it:

1. Click **folder1**.

2. Click **Create folder**.

3. Enter **folder2** for **Name** and **click Create**.

4. Click **folder2**.

5. Click **Upload > Upload files**.

6. In the file dialog, navigate to the screenshot that you downloaded and select it.

After the upload completes, you should see the file name and information about the file, such as its size and type.

## Task 5. Delete a folder
1. Click the arrow next to **Bucket details** to return to the buckets level.

2. Select the bucket.

3. Click on the **Delete** button.

4. In the window that opens, type DELETE to confirm the deletion of the folder.

5. Click **Delete** to permanently delete the folder and all objects and subfolders in it.

## Reference :
Google Cloud Skill Boost :
[Cloud Storage: Qwik Start - Cloud Console](https://www.cloudskillsboost.google/focuses/1760?catalog_rank=%7B%22rank%22%3A2%2C%22num_filters%22%3A0%2C%22has_search%22%3Atrue%7D&parent=catalog&search_id=56272911)
