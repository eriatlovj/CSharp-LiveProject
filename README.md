# CSharp-LiveProject
The Tech Academy C# Live Project

# Introduction

For this project, I was working with a team developing a full scale MVC Web Application in Microsoft Visual Studio, utilizing Entity Framework code first. I got to experience using the AGILE system as well as Microsoft Azure DevOps to work effectively in a team. Each working day, a stand-up meeting was held, in which each member of the team would explain what user story they were working on, what they had accomplished the previous day, and what they were currently working on, as well as if the team members have any issues or roadblocks that they were facing. There was also a sprint planning meeting at the beginning of the project, as well as a retrospective at the end of each week. I mostly worked on stories for the back end of the application. This two week sprint helped me to develop the skills necessary to succeed in a career as a web developer.

The application is in its early stages and is not yet publically live, but you can see below the snippets of the code that I worked on.

# Creating the Display Links Class

This Story was the first one I worked on and it was fairly simple, which I also thought was appropriate for me to start with. I was tasked to create the Display Links Class, create a scaffolded controller and views.

# Display Links Class

```

    public class DisplayLinks
    {
        [Key]
        public int LinkId { get; set; }
        public string Name { get; set; }
        public string Text { get; set; }
        public string Link { get; set; }
    }

```

# Creating the Subscriber Area

A Subscriber Area was created in order for the subscribers to have a seperate area for the sections that they will see. The Subscriber Area will be utilized for managing the subscribers accounts.

# Subscriber Class

```

    public class SubscribersAreaRegistration : AreaRegistration 
    {
        public override string AreaName 
        {
            get 
            {
                return "Subscribers";
            }
        }

        public override void RegisterArea(AreaRegistrationContext context) 
        {
            context.MapRoute(
                "Subscribers_default",
                "Subscribers/{controller}/{action}/{id}",
                new { action = "Index", id = UrlParameter.Optional }
            );
        }
    }

```


# Creating the Subscriber Class

For this story, the Subscriber class has a 1 to 1 relationship with the User class and I utilized a virtual property for both of the classes. A controller and view section were added as well.

# Subscriber Class

```
    public class Subscriber
    {
        [Key]
        [ForeignKey("SubscriberPerson")]
        public string SubscriberId { get; set; }
        public bool CurrentSubscriber { get; set; }
        public bool HasRenewed { get; set; }
        public bool Newsletter { get; set; }
        public bool RecentDonor { get; set; }
        [DataType(DataType.Date)]
        [DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
        public DateTime LastDonated { get; set; }
        public decimal? LastDonationAmt { get; set; }
        public string SpecialRequests { get; set; }
        public string Notes { get; set; }
        [Required]
        public virtual ApplicationUser SubscriberPerson { get; set; }
    }

```

```

    public class ApplicationUser : IdentityUser
    {
        public async Task<ClaimsIdentity> GenerateUserIdentityAsync(UserManager<ApplicationUser> manager)
        {
            // Note the authenticationType must match the one defined in CookieAuthenticationOptions.AuthenticationType
            var userIdentity = await manager.CreateIdentityAsync(this, DefaultAuthenticationTypes.ApplicationCookie);
            // Add custom user claims here
            return userIdentity;
        }

        public virtual Subscriber SubscriberPerson { get; set; }
    }
    

```

# Creating the Season Manager Class

This story that I worked on is similar with creating the Subscriber class. It also has a 1 to 1 relationship with the User class.  I also utilized a virtual property for both of the classes. A controller and view section were added as well.

```

public class SeasonManager
    {
        [Key]
        [ForeignKey("SeasonManagerPerson")]
        public string SeasonManagerId { get; set; }
        public int NumberSeats { get; set; }
        public bool BookedCurrent { get; set; }
        public string FallProd { get; set; }
        [DataType(DataType.DateTime)]
        [DisplayFormat(DataFormatString = "{0:MM-dd-yyyy hh:mm tt}", ApplyFormatInEditMode = true)]
        public DateTime FallTime { get; set; }
        public bool BookedFall { get; set; }
        public string WinterProd { get; set; }
        [DataType(DataType.DateTime)]
        [DisplayFormat(DataFormatString = "{0:MM-dd-yyyy hh:mm tt}", ApplyFormatInEditMode = true)]
        public DateTime WinterTime { get; set; }
        public bool BookedWinter { get; set; }
        public string SpringProd { get; set; }
        [DataType(DataType.DateTime)]
        [DisplayFormat(DataFormatString = "{0:MM-dd-yyyy hh:mm tt}", ApplyFormatInEditMode = true)]
        public DateTime SpringTime { get; set; }
        public bool BookedSpring { get; set; }
        [Required]
        public virtual ApplicationUser SeasonManagerPerson { get; set; }

    }
    
```

```

    public class ApplicationUser : IdentityUser
    {
        public async Task<ClaimsIdentity> GenerateUserIdentityAsync(UserManager<ApplicationUser> manager)
        {
            // Note the authenticationType must match the one defined in CookieAuthenticationOptions.AuthenticationType
            var userIdentity = await manager.CreateIdentityAsync(this, DefaultAuthenticationTypes.ApplicationCookie);
            // Add custom user claims here
            return userIdentity;
        }

        public virtual SeasonManager SeasonManagerPerson { get; set; }
    }

```

# Modifying the Content Section Class

This was a story that I previously worked on, but unfortunately I did not use a custom Enum for the ContentType. I made changes to the model and added a drop down list for the options for creating a new ContentType object. The Content Section Class tracks the ID number of the section that it is meant to display, it doesn't really track the object itself. This allows greater flexibility of use of that attribute so it can refer to multiple types of objects.

# Model

```
    public class ContentSection
    {
        [Key]
        public int SectionId { get; set; }
        public ContentType ContentType { get; set; }
        public int ContentId { get; set; }
        public string CssId { get; set; }

    }
    public enum ContentType
    {
        Production,
        Sponsor,
        Info,
        Links
    }
    
```
# Create View

```
        <div class="form-group">
            @Html.LabelFor(model => model.ContentType, htmlAttributes: new { @class = "control-label col-md-2" })
            <div class="col-md-10">
                @Html.EnumDropDownListFor(model => model.ContentType, htmlAttributes: new { @class = "form-control" })
                @Html.ValidationMessageFor(model => model.ContentType, "", new { @class = "text-danger" })
            </div>
        </div>
        
```

# Creating a drop down list

For this story, I was tasked to create a list from the existing database for the user to select which existing User to attach to a new model that was being created. The database was queried to access the list of Users and then the Select list was created as part of the Create and Edit pages. A dummy data was created for the database in order to test the functionality of this work.

# Controller

```
public ActionResult Create([Bind(Include = "SeasonManagerId,NumberSeats,BookedCurrent,FallProd,FallTime,BookedFall,WinterProd,WinterTime,BookedWinter,SpringProd,SpringTime,BookedSpring")] SeasonManager seasonManager)
        {
            ModelState.Remove("SeasonManagerPerson");
            string userId = Request.Form["dbUsers"].ToString();

            if (ModelState.IsValid)
            {
                ViewData["dbUsers"] = new SelectList(db.Users.ToList(), "Id", "UserName");
                seasonManager.SeasonManagerPerson = db.Users.Find(userId);
                db.SeasonManagers.Add(seasonManager);
                db.SaveChanges();
                return RedirectToAction("Index");
            }

            return View(seasonManager);
        }
 ```
 # Create View
 
 ```
     <div class="form-group">
        @Html.LabelFor(model => model.SeasonManagerPerson, htmlAttributes: new { @class = "control-label col-md-2" })
        <div class="col-md-10">
            @Html.DropDownList("dbUsers",(IEnumerable<SelectListItem>)ViewData["dbUsers"])
        </div>
    </div>
