options(rpubs.upload.method = "internal")
### This is the system Rprofile file. It is always run on startup.
### Additional commands can be placed in site or user Rprofile files
#
#  Copyright (C) 1995-2012 The R Core Team
### (see ?Rprofile).

### Notice that it is a bad idea to use this file as a template for
### personal startup files, since things will be executed twice and in
### the wrong environment (user profiles are run in .GlobalEnv).

.GlobalEnv <- globalenv()
attach(NULL, name = "Autoloads")
.AutoloadEnv <- as.environment(2)
assign(".Autoloaded", NULL, envir = .AutoloadEnv)
T <- TRUE
F <- FALSE
R.version <- structure(R.Version(), class = "simple.list")
version <- R.version            # for S compatibility

## for backwards compatibility only
R.version.string <- R.version$version.string

## NOTA BENE: options() for non-base package functionality are in places like
##            --------- ../utils/R/zzz.R

options(keep.source = interactive())
options(warn = 0)
# options(repos = c(CRAN="@CRAN@"))
# options(BIOC = "http://www.bioconductor.org")

options(timeout = 60)
options(encoding = "native.enc")
options(show.error.messages = TRUE)
## keep in sync with PrintDefaults() in  ../../main/print.c :
options(scipen = 0)
options(max.print = 99999)# max. #{entries} in internal printMatrix()
options(add.smooth = TRUE)# currently only used in 'plot.lm'
options(stringsAsFactors = TRUE)
if(!interactive() && is.null(getOption("showErrorCalls")))
    options(showErrorCalls = TRUE)

local({dp <- Sys.getenv("R_DEFAULT_PACKAGES")
       if(identical(dp, "")) # marginally faster to do methods last
           dp <- c("datasets", "utils", "grDevices", "graphics",
                   "stats", "methods")
       else if(identical(dp, "NULL")) dp <- character(0)
       else dp <- strsplit(dp, ",")[[1]]
       dp <- sub("[[:blank:]]*([[:alnum:]]+)", "\\1", dp) # strip whitespace
       options(defaultPackages = dp)
    })

## Expand R_LIBS_* environment variables.
Sys.setenv(R_LIBS_SITE =
           .expand_R_libs_env_var(Sys.getenv("R_LIBS_SITE")))
Sys.setenv(R_LIBS_USER =
           .expand_R_libs_env_var(Sys.getenv("R_LIBS_USER")))

.First.sys <- function()
{
    for(pkg in getOption("defaultPackages")) {
        res <- require(pkg, quietly = TRUE, warn.conflicts = FALSE,
                       character.only = TRUE)
        if(!res)
            warning(gettextf('package %s in options("defaultPackages") was not found', sQuote(pkg)),
                    call.=FALSE, domain = NA)
    }
}

.OptRequireMethods <- function()
{
      if("methods" %in% getOption("defaultPackages")) {
        res <- require("methods", quietly = TRUE, warn.conflicts = FALSE,
                       character.only = TRUE)
        if(!res)
            warning('package "methods" in options("defaultPackages") was not found', call.=FALSE)
    }
}

if(nzchar(Sys.getenv("R_BATCH"))) {
    .Last.sys <- function()
    {
        cat("> proc.time()\n")
        print(proc.time())
    }
    ## avoid passing on to spawned R processes
    ## A system has been reported without Sys.unsetenv, so try this
    try(Sys.setenv(R_BATCH=""))
}
###-*- R -*-

## this will break if R is on a network share
.Library <- file.path(chartr("\\", "/", R.home()), "library")
.Library.site <- Sys.getenv("R_LIBS_SITE")
.Library.site <- if(!nchar(.Library.site)) file.path(R.home(), "site-library") else unlist(strsplit(.Library.site, ";"))
.Library.site <- .Library.site[file.exists(.Library.site)]

if(!nzchar(Sys.getenv("R_LIBS_USER")))
    Sys.setenv(R_LIBS_USER=
               file.path(Sys.getenv("R_USER"), "R",
                         "win-library",
                         paste(R.version$major,
                               sub("\\..*$", "", R.version$minor),
                               sep=".")
                         ))

invisible(.libPaths(c(unlist(strsplit(Sys.getenv("R_LIBS"), ";")),
                      unlist(strsplit(Sys.getenv("R_LIBS_USER"), ";"))
                      )))

local({
    popath <- Sys.getenv("R_TRANSLATIONS", "")
    if(!nzchar(popath)) {
        paths <- file.path(.libPaths(), "translations", "DESCRIPTION")
        popath <- dirname(paths[file.exists(paths)][1])
    }
    bindtextdomain("R", popath)
    bindtextdomain("R-base", popath)
    bindtextdomain("RGui", popath)
    assign(".popath", popath, .BaseNamespaceEnv)
})

if(nzchar(Sys.getenv("R_PAPERSIZE"))) {
    options(papersize = Sys.getenv("R_PAPERSIZE"))
} else {
    if(grepl("(canada|united.states)", Sys.getlocale("LC_MONETARY"),
             ignore.case = TRUE)) options(papersize = "letter")
    else options(papersize = "a4")
}

options(pager = if(length(grep("--ess", commandArgs()))) "console" else "internal",
        useFancyQuotes = (.Platform$GUI == "Rgui"),
        pdfviewer = Sys.getenv("R_PDFVIEWER", file.path(R.home("bin"), "open.exe")))

if(.Platform$GUI == "Rgui")
    Sys.setenv(GFORTRAN_STDOUT_UNIT = "-1", GFORTRAN_STDERR_UNIT = "-1")

local({
    br <- Sys.getenv("R_BROWSER", NA_character_)
    if(!is.na(br)) options(browser = br)
    tests_startup <- Sys.getenv("R_TESTS")
    if(nzchar(tests_startup)) source(tests_startup)
})