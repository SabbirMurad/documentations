# Welcome to Actix Template

Actix is a great rust framework for handling http and https servers. This template is created so the we can start a new web project in rust without worrying about setting up the whole server. This template uses rust actix for the server, tera to handle imports within the html files. for the database uses MongoDb, Sqlite and Redis. For the frontend section used vanilla Javascript, html + (html web component) and scss.

### Here below is the structure of the actix template

actix_template/
│
├── .dev_tls/                   # Folder to hold locally created fake tls certificate
├── assets/                     
│   ├── css/                    # Css files that are auto generated from scss file
│   ├── favicon/                # Website favicons
│   ├── icon/                   # Any svg icons used in the website
│   ├── image/                  # Any png.jpeg,webp used in the website
│   ├── js/                     # Js files
│   └── scss/                   # Scss files
├── components/                 
│   └── my-comp/                # The folder for your each component
│       ├── style.css           # Css for this specific component
│       └── module.css          # Js for this specific component
├── docs/                       # Detailed documentation about the project using mkdocs
├── pages/                      # All the html pages that will be served in the website
│   ├── common/                 # Holds some common html that is needed in all pages
│   ├── error/                  # The 400,404,401 pages
│   ├── common.html             # A file to import all the common files in, then this gets imported in other pages
│   └── home.html               # Starting page of the website
├── src/                        
│   ├── builtins/               # Holds big modules that gets used in all projects
│   ├── handler/                # The api handler functions
│   ├── integrations/           # The 3rd party services used the project
│   ├── middleware/             # Middleware functions used in some routes
│   ├── routes/                 # The routes for the api
│   ├── model/                  # Model for the collections in Mongodb
│   ├── utils/                  # Util functions that are specific this this project
│   └── main.rs                 # Starting point of the application
├── .env                        # Environment variables for production
├── .env.dev                    # Environment variables demo for development
├── .env.release                # Environment variables demo for production
├── .gitignore                  # Git ignore file to exclude unnecessary files
└── README.md                   # Project basic documentation

We would learn more about each of these folders in the upcoming sections

**Please make sure you have the following items installed in your machine before starting the project**

1. [Rust](https://www.rust-lang.org/tools/install)
2. [Windows Subsystem for Linux (WSL)](./install_wsl.md)
2. [Redis inside WSL](https://linuxize.com/post/how-to-install-and-configure-redis-on-ubuntu-20-04/)
2. [MongoDB inside WSL](https://www.mongodb.com/docs/manual/tutorial/install-mongodb-on-ubuntu/)