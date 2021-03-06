# Tutorial: How to Upload Image Files from OrgChart JS to the server - ASP.NET Core

To upload image from the edit form you have to implement imageuploaded event hadler. Here is an example


        chart.editUI.on('imageuploaded', function (sender, file, input) {
            var formData = new FormData();
            formData.append('file', file);           

            $.ajax({
                type: "POST",
                url: "@Url.Action("UploadImage")",
                data: formData,
                dataType: 'json',
                contentType: false,
                processData: false,
                success: function (data) {
                    input.value = data.url;
                },
                error: function (error) {
                    alert(error);
                }
            });
        });
        
imageUploadHandler has 3 parameters:
- sender - the editUI object
- file - the actual file that is going to be uploaded 
- input - this is the HTML input element from the edit form

The next step is to create upload method on the server 

        [HttpPost]
        public async Task<IActionResult> UploadImage(IFormFile file)
        {
            var uniqueFileName = GetUniqueFileName(file.FileName);
            var uploads = Path.Combine(hostingEnvironment.WebRootPath, "uploads");
            var filePath = Path.Combine(uploads, uniqueFileName);
            
            if (file.Length > 0)
            {
                using (var stream = new FileStream(filePath, FileMode.Create))
                {
                    await file.CopyToAsync(stream);
                }
            }

            return Ok(new { url = Url.Content("~/uploads/" + uniqueFileName) });
        }


[BALKANGraph](https://balkangraph.com)
