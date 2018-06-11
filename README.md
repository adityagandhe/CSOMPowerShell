## Welcome to GitHub Pages

You can use the [editor on GitHub](https://github.com/adityagandhe/CSOMPowerShell/edit/master/README.md) to maintain and preview the content for your website in Markdown files.

Whenever you commit to this repository, GitHub Pages will run [Jekyll](https://jekyllrb.com/) to rebuild the pages in your site, from the content in your Markdown files.

### Markdown

Markdown is a lightweight and easy-to-use syntax for styling your writing. It includes conventions for

```markdown
Syntax highlighted code block


#add the scripts
Add-Type -Path "C:\Program Files\Common Files\microsoft shared\Web Server Extensions\16\ISAPI\Microsoft.SharePoint.Client.dll"
Add-Type -Path "C:\Program Files\Common Files\microsoft shared\Web Server Extensions\16\ISAPI\Microsoft.Sharepoint.Client.Runtime.dll"

Add-Type -Path "C:\Program Files\Common Files\microsoft shared\Web Server Extensions\16\ISAPI\Microsoft.Sharepoint.Client.UserProfiles.dll"

#get authentication
$username = "aditya@oystera803.onmicrosoft.com"
$site = "https://oystera803.sharepoint.com"
$password = "htc_mozart7"
$securePassword = ConvertTo-SecureString $password -AsPlainText -Force 
$clientcontext = New-Object Microsoft.SharePoint.Client.ClientContext($site)
$credentials = New-Object Microsoft.SharePoint.Client.SharePointOnlineCredentials($username, $securePassword)

$clientcontext.Credentials = $credentials

$web = $clientcontext.Web
$childwebs = $web.Webs
$clientcontext.Load($web)
$clientcontext.Load($childwebs)
$clientcontext.ExecuteQuery()

$script:stuff = @()
function processfunction($child) { 
   
    $lists = $child.lists
    $clientcontext.Load($child)
    $clientcontext.Load($child.Webs)
    $clientcontext.Load($lists)
    $clientcontext.ExecuteQuery()
    write-host "fetching data for site"$child.Url
    foreach ($list in $lists) {
        #Write-Host $list.Title  $list.ItemCount
        $listItems = $list.GetItems([Microsoft.SharePoint.Client.CamlQuery]::CreateAllItemsQuery())
          
        $clientcontext.load($lists)
        $clientcontext.load($listItems)
        $clientcontext.ExecuteQuery()

        if ( $list.Title -ne "Site Assets") {


       
            foreach ($listItem in $listItems) {

                $lookup = [Microsoft.SharePoint.Client.FieldLookupValue]$listItem["Author"]
                $name = $lookup.LookupValue
                $lookup1 = [Microsoft.SharePoint.Client.FieldLookupValue]$listItem["Editor"] 
                $modified = $lookup1.LookupValue
                #  $listItem["Title"] = "aa"  
                #  $listItem.Update()  
                $clientcontext.load($listItem)      
                $clientcontext.executeQuery() 
    
                Write-Host "ID - " $listItem.Id "Modified" $modified  "Modified By" $name "Title" $listItem["Title"]
                $obj = new-object PSObject
                $obj | add-member -membertype NoteProperty -name "List" -value $list.Title
                $obj | add-member -membertype NoteProperty -name "Id" -value $listItem.Id
                $obj | add-member -membertype NoteProperty -name "Title" -value $listItem["Title"]
                $obj | add-member -membertype NoteProperty -name "Modified" -value $modified
                $obj | add-member -membertype NoteProperty -name "Created" -value $name
                $script:stuff += $obj
                $stuff | export-csv C:\Users\Aditya\Desktop\books\code\Poweshell\nsample.csv -notypeinformation
            }

        }


    }

    foreach ($children in $child.Webs) {
        processfunction $children
    }


}
write-host "Completed"  
   
processfunction $web



- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
```

For more details see [GitHub Flavored Markdown](https://guides.github.com/features/mastering-markdown/).

### Jekyll Themes

Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/adityagandhe/CSOMPowerShell/settings). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://help.github.com/categories/github-pages-basics/) or [contact support](https://github.com/contact) and we’ll help you sort it out.
