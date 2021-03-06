# Open Data Maker
[![Build Status](https://circleci.com/gh/18F/open-data-maker/tree/dev.svg?style=svg)](https://circleci.com/gh/18F/open-data-maker/tree/dev)

The goal of this project is to make it easy to turn a lot of potentially large
csv files into open data via an API and the ability for people to download
smaller csv files with a subset of the data.

Preliminary research suggests that open data users (journalists and others)
actually know how to work with spreadsheets really well, but a lot of the
data sets that we have in government are huge.

The first version of this project will allow us to host a website for an
agency with a specific set of csv files, which are deployed with the app.
This will allows us to deploy more quickly since there will be a lower risk
security profile than if an agency could upload the CSV files (which might
be a nice longer term feature).


## Install and Run the App (as a developer)

See our [Installation Guide](INSTALL.md)

## How this works

By default, data will be loaded from /sample-data when you run `rake import`

* [cities100.csv](sample-data/cities100.csv) - dataset of 100 most populous cities in the US
* [data.yaml](sample-data/data.yaml) - configuration for
  * index name *city-data*
  * api endpoint name *cities*
  * how columns are mapped to fields in json output
  * data types
  * unique columns *name*  

When you run the app, you can query the dataset via json API, like: /cities?name=Chicago

* http://localhost:3000/cities?name=Chicago
* http://localhost:3000/cities?name=Chicago&state=IL
* http://localhost:3000/cities?state=NY,MA
* http://localhost:3000/cities?state=CA&fields=name,size

To use your own data, you can set a different directory, for example:

```
export DATA_PATH='./data'
```

1. Put csv files into /data
1. Import files from /data: ```rake import``` (or restart the app)
   1. There can be multiple files (must end in .csv)
   1. Optional [data.yaml](sample-data/data.yaml) file that specifies  index name, API endpoint, file list, and a dictionary of column -> field name mapping and types
        1. Optionally import all the columns, not just ones specified in dictionary (see example: [import: all](spec/fixtures/import_with_options/data.yaml))
        1. If data.yaml not provided, all fields and fields will be imported with folder or bucket name used as the API endpoint (name is 'slugified' with dashes replacing spaces)
1. api endpoint to get the data /api=endpoint?field_or_column_name=value

## More Configuration Options

Often while you are developing an API and data dictionary,
it is helpful to include all the columns in the csv.  If you add the following to
data.yaml, the field names and types from the dictionary will be used and any
unspecified columns will simply use the column name as the field name.

```
options:
  columns: all
```

You can use the dictionary to provide nice errors to developers who use the API.
This can be used in conjunction with the above ```columns: all``` which will
make it so that columns that are not referenced in the dictionary are not
searchable, but will make it so that unspecified fields cause errors to be
reported.

```
options:
  search: dictionary_only
```

Also for debugging, you can limit the number of files that will be imported.  This is helpful when the import process is time consuming because you have many, many files, but can test format changes with a subset of the files.

```
options:
  limit: 4
```



## Help Wanted

1. Try out importing multiple data sets with different endpoints and data.yaml configuration
2. Take a look at our [open issues](https://github.com/18F/open-data-maker/issues) and our [Contribution Guide](CONTRIBUTING.md)

## More Info

Here's how it might look in the future:

![overview of data types, prompt to download data, create a custom data set, or look at API docs](/doc/data-overview.png)


![Download all the data or make choices to create a csv with a subset](/doc/csv-download.png)

### Acknowledgements
Zipcode latitude and longitude provided by [GeoNames](http://www.geonames.org/) under under a [Creative Commons Attribution 3.0 License](http://creativecommons.org/licenses/by/3.0/).

### Public domain

Except as noted above, this project is in the worldwide [public domain](LICENSE.md). As stated in [CONTRIBUTING](CONTRIBUTING.md):

> This project is in the public domain within the United States, and copyright and related rights in the work worldwide are waived through the [CC0 1.0 Universal public domain dedication](https://creativecommons.org/publicdomain/zero/1.0/).
>
> All contributions to this project will be released under the CC0 dedication. By submitting a pull request, you are agreeing to comply with this waiver of copyright interest.
