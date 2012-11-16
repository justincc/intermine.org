Template Queries
================================

There are several processes run after the data loading is completed, one of which the objectstore summarisation.  This step counts the number of objects of particular classes, identifies any empty references/collections and collects values to be appear in dropdowns in the query builder and templates. The summarisation process also constructs the indexes needed for "type-ahead" autocompletion, this is configured by adding entries to the [source:trunk/bio/tutorial/malariamine/dbmodel/resources/objectstoresummary.config.properties objectstoresummary.config.properties].

'''Dropdowns'''

Some fields have only a few different values, and are represented as dropdowns on forms so that users may see all possible values. You can set the maximum number of values to display, the default is 200.

Also, if your database has tables that should be ignored, you can set this too:
{{{
# in objectstoresummary.config.properties
ignore.counts=org.intermine.model.bio.GOAnnotation.withText org.intermine.model.bio.Location.subject
}}}

'''Auto-completion'''

Fields in template queries and the QueryBuilder can have type-ahead autocompletion to assist in selecting valid terms. As you start to type, possible matches are fetched from the database; the text you have typed can match anywhere within the terms and multiple words can be matched. This is particularly useful for ontology terms or protein domain names.

You can set up autocompletion by completing these steps:

 1. Add all fields you want to be autocompleted to this file, like so:
    {{{
# in MINE_NAME/dbmodel/resources/objectstoresummary.config.properties
org.intermine.model.bio.Disease.autocomplete = description
}}}

 2. Add the postprocess to your `MINE_NAME/project.xml` file.
    {{{
  <post-processing>    
    <post-process name="create-autocomplete-index"/>
  </post-processing>
}}}

 3. In the {{{/postprocess}}} directory, run this command:
    {{{
# in malariamine/postprocess
% ant -Daction=create-autocomplete-index
}}}
    * This process will add all fields set in this properties file to the autocompletion index.

Now, when you release your webapp, fields you've configured will suggest similar terms as users are typing in the QueryBuilder or the template form.

Template forms
------------------

'''dropdowns'''

When constraining a field instead of a text field you may see a dropdown.  This dropdown automatically appears if there are less than 200 unique possible values in the field.  

The unique values are calculated in the [wiki:PostProcessing#summarise-objectstore summarise-objectstore] post process.  

Note that dropdowns only appear for the EQUALS operator.  If the operator is LIKE, a text field will appear to enable the use of wildcards.

Multiselect - if the field has a dropdown, users can use the ONE OF operator to select more than one option

You can make templates run faster by generating precomputes.  You can also "summarise" dropdowns, eg. make it so only the relevant values appear.  See PrecomputedTables for details.

'''autocomplete'''

You can set a form field to autocomplete as the user is typing.  See ObjectStoreSummaryProperties for details on how to set these properties.  

The autocomplete index is created in the [wiki:PostProcessing#summarise-objectstore summarise-objectstore] post process.  

'''optional constraints'''

To make a template constraint optional:

   1. edit the template in the query builder
   1. click on the padlock next to the constraint
   1. select optional:
      {{{
Required - the user must supply a value
Optional: ON - optional and ON by default
Optional: OFF - optional and OFF by defaul
}}}


Templates page
------------------

To have templates appear on the templates page, create a template as a SuperUser and [wiki:Tagging tag] the template with the "im:public" tag.

The templates are sorted by most popular first.  If the user is logged in the user's most popular templates are shown first.
