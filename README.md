<div id="pagewrap">

    <div id="header">
        <h1>Apache Solr Documentation</h1>
        <p>This document is designed to set up a local instance of Solr 4.5, indexing a site to Solr with Nutch 1.7, then finally integrating that to the front-end with JavaScript framework AJAX Solr. By the end of the tutorial you will be able to perform a global search of all web pages within your domain of <em>www.mywebsite.com</em>.</p>

        <ul>
        <li>Apache Solr 4.5</li>
        <li>Apache Nutch 1.7</li>
        <li>AJAX Solr</li>
        </ul>
    </div>

    <div id="content">
<!-- Apache Solr 4.5 ************************************************************************************************************************************************************************************************************ -->   
        <h2 id="apacheSolr">Step 1: Apache Solr 4.5</h2>
        <ol>
            <li><a href="http://lucene.apache.org/solr/downloads.html" target="_blank">Download</a> Solr 4.5 into its own directory (i.e. /Users/johnsmith/solr).</li>

            <li>Unzip the download, then copy the child directory, 'example' and rename it to, 'mywebsite'.</li>

            <li>On your newly created directory, open the <strong>core.properties</strong> file and rename the collection:
<pre>
    <code>
    /* PROJECT DIRECTORY */
    johnsmith > solr > mywebsite > solr > collection1 > core.properties

    /* BEFORE */
    name=collection1

    /* AFTER */     
    name=mywebsite
    </code>
</pre>                  
            </li>

            <li>Move up one directory and rename directory, 'collection1' to 'mywebsitesearch':
<pre>
    <code>
    /* OLD DIRECTORY */
    johnsmith > solr > mywebsite > solr > collection1

    /* NEW DIRECTORY */
    johnsmith > solr > mywebsite > solr > mywebsitesearch
    </code>
</pre>                  
            </li>           

            <li>To be safe, duplicate <strong>schema.xml</strong>, renaming the original as <strong>_schema.xml</strong> and keeping a new copy called <strong>schema.xml</strong>
<pre>
    <code>
    /* PROJECT DIRECTORY */
    johnsmith > solr > mywebsite > solr > mywebsitesearch > conf > _schema.xml
    johnsmith > solr > mywebsite > solr > mywebsitesearch > conf > schema.xml
    </code>             
</pre>                  
            </li>

            <li>Initialize the Solr instance by entering the root of 'mywebsite' directory. On the command line run the following:
<pre>
    <code>
    $ java -jar start.jar
    </code>             
</pre>                  
            </li>
            <li>Load the following URL to your browser, <a href="http://localhost:8983/solr/" target="_blank">http://localhost:8983/solr/</a></li>
        </ol>

        <h3>HINTS:</h3>

        <ol>
            <li>Delete all documents from the Solr index:
<pre>
    <code>
    1.) Ensure your SOLR instance is running.
    2.) In a new terminal run the following two commands,
        $ curl http://localhost:8983/solr/mywebsite/update --data '&lt;delete&gt;&lt;query&gt;*:*&lt;/query&gt;&lt;/delete&gt;' -H 'Content-type:text/xml; charset=utf-8'  
        $ curl http://localhost:8983/solr/mywebsite/update --data '&lt;commit/&gt;' -H 'Content-type:text/xml; charset=utf-8'
    </code>             
</pre>                  
            </li>

            <li>Stop the Solr instance:
<pre>
    <code>
    1.) Go to the current terminal window that has the Solr instance and press the following two keys:
        CTRL + C
    </code>             
</pre>                  
            </li>

            <li>Validate your queries by opening <strong>solr.log</strong>:
<pre>
    <code>
    /* PROJECT DIRECTORY */
    johnsmith > solr > mywebsite > logs > solr.log
    </code>             
</pre>                  
            </li>
        </ol>       

<!-- Apache Nutch 1.7 ************************************************************************************************************************************************************************************************************ -->
        <h2 id="apacheNutch">Step 2: Apache Nutch 1.7</h2>
        <ol>
            <li><a href="http://www.motorlogy.com/apache/nutch/" target="_blank">Download</a> Nutch 1.7 into its own directory (i.e. /Users/johnsmith/nutch).</li>

            <li>Open <strong>nutch-site.xml</strong> and add the following property nodes as a child of the configuration node:
<pre>
    <code>
    /* PROJECT DIRECTORY */
    johnsmith > nutch > conf > nutch-site.xml

    /* PROPERTIES TO ADD */
    &lt;property&gt;
        &lt;name&gt;http.agent.name&lt;/name&gt;
        &lt;value&gt;My mywebsite Spider&lt;/value&gt;
    &lt;/property&gt;

    &lt;property&gt;
      &lt;name&gt;fetcher.threads.per.queue&lt;/name&gt;
         &lt;value&gt;10&lt;/value&gt;
         &lt;description&gt;&lt;/description&gt;
    &lt;/property&gt;

    &lt;property&gt;
      &lt;name&gt;fetcher.threads.per.host&lt;/name&gt;
         &lt;value&gt;10&lt;/value&gt;
         &lt;description&gt;&lt;/description&gt;
    &lt;/property&gt; 

    &lt;property&gt;
        &lt;name&gt;plugin.includes&lt;/name&gt;
        &lt;value&gt;protocol-http|urlfilter-regex|urlfilter-suffix|parse-(html|tika)|index-(basic|anchor)|indexer-solr|urlfilter-validator&lt;/value&gt;
    &lt;/property&gt;       


    </code>             
</pre>                  
            </li>

            <li>Replace the last line of <strong>regex-urlfilter.txt</strong> to be specific to www.mywebsite.com (both secure and non-secure protocols):
<pre>
    <code>
    /* PROJECT DIRECTORY */
    johnsmith > nutch > conf > regex-urlfilter.txt

    /* REPLACE WITH */
    ++^(https?://)?www\.mywebsite.com
    </code>             
</pre>                  
            </li>

            <li>Create a 'urls' directory in the project root and create a <strong>seed.txt</strong> file:
<pre>
    <code>  
    /* ON COMMAND LINE */
    $ mkdir -p urls
    $ cd urls
    $ touch seed.txt

    /* EXPECTED DIRECTORY */
    johnsmith > nutch > urls > seed.txt 
    </code>             
</pre>                                      
            </li>

            <li>Edit the <strong>seed.txt</strong> with the following URL:
<pre>
    <code>  
    http://www.mywebsite.com/
    </code>             
</pre>                  
            </li>

            <li>Overwrite the <strong>schema.xml</strong> with the contents from <strong>schema-solr4.xml</strong>:
<pre>
    <code>  
    /* OVERWRITE THE CONTENTS OF THIS: */
    johnsmith > nutch > conf > schema.xml

    /* WITH THE CONTENTS OF THIS: */
    johnsmith > nutch > conf > schema-solr4.xml
    </code>             
</pre>                  
            </li>

            <li>Update the newly pasted <strong>schema.xml</strong> with the following:
<pre>
    <code>  
    /* PROJECT DIRECTORY */
    johnsmith > nutch > conf > schema-solr4.xml

    /* Update the version of nutch from 1.5 to 1.7 per line 28 */
    &lt;schema name="nutch" version="1.7"&gt;

    /* Update the attribute type from url to string per line 312 */
    &lt;field name="host" type="string" stored="false" indexed="true"/&gt; 

    /* Update the attribute type from text_general to string per line 324 */
    &lt;field name="anchor" type="string" stored="true" indexed="true" multiValued="true"/&gt; 

    /* Add a new field node per line 352 */
    &lt;field name="_version_" type="long" indexed="true" stored="true" multiValued="false"/&gt;

    /* Update uniqueKey node’s value from id to url per line 354 */
    &lt;uniqueKey&gt;url&lt;/uniqueKey&gt;  
    </code>             
</pre>                  
            </li>

            <li>Copy/paste your updated <strong>schema.xml</strong> from Nutch to the Solr instance of <strong>schema.xml</strong>.</li>

            <li>Add the following declarations to <strong>suffix-urlfilter.txt</strong> in order to not crawl/index documents:
<pre>
    <code>  
    /* PROJECT DIRECTORY */
    johnsmith > nutch > conf > suffix-urlfilter.txt

    # documents
    .pdf
    .doc
    .docx
    .xsl
    .xslx
    </code>             
</pre>                  
            </li>

            <li>Set your JAVA_HOME variable in terminal:
<pre>
    <code>  
    $ export JAVA_HOME=`/usr/libexec/java_home -v 1.6`
    </code>             
</pre>                  
            </li>

            <li>Start the crawl on Nutch and assign the address of the Solr server that will be responsible for the indexed data:
<pre>
    <code>  
    $ bin/nutch crawl urls -solr http://localhost:8983/solr/mywebsite -depth 3 -topN 10      00
    </code>             
</pre>                                  
            </li>
        </ol>


        <h3>HINTS:</h3>

        <ol>
            <li>Delete duplicates after a crawl:
<pre>
    <code>
    1.) In your current terminal window for Nutch run the following,
        $ bin/nutch solrdedup http://localhost:8983/solr/mywebsite   
    </code>             
</pre>                  
            </li>
            <li>Ensure the <strong>schema.xml</strong> is matching for both Nutch 1.7 and Solr 4.5 (see step 8).        
            </li>
        </ol>       

        <!-- AJAX Solr ************************************************************************************************************************************************************************************************************ -->
        <h2 id="ajaxSolr">Step 3: AJAX Solr</h2>
        <p>Integration of this framework assumes a dependency on jQuery.</p>
        <ol>
            <li><a href="https://github.com/evolvingweb/ajax-solr" target="_blank">Download</a> AJAX Solr into its own directory (i.e. /Users/johnsmith/ajax-solr).


            <li>Assuming you have a <strong>css directory</strong> in place, paste the following CSS link tag before &lt;/head&gt; of your project template:
<pre>
    <code>
    &lt;link rel="stylesheet" href="css/search.css"&gt;
    </code>
</pre>                  
            </li>

            <li>Assuming you have an <strong>images directory</strong> in place, add 'ajax-loader.gif' as a file of that directory.</li>

            <li>Assuming you have a <strong>js directory</strong> load the JavaScript files to that directory and paste the following script tags after &lt;/footer&gt; of your project template:
<pre>
    <code>
    &lt;script src="js/search.js"&gt;&lt;/script&gt;
    &lt;script src="js/Core.js"&gt;&lt;/script&gt;
    &lt;script src="js/AbstractManager.js"&gt;&lt;/script&gt;
    &lt;script src="js/Manager.jquery.js"&gt;&lt;/script&gt;
    &lt;script src="js/Parameter.js"&gt;&lt;/script&gt;
    &lt;script src="js/ParameterStore.js"&gt;&lt;/script&gt;
    &lt;script src="js/AbstractWidget.js"&gt;&lt;/script&gt;
    &lt;script src="js/ResultWidget.js"&gt;&lt;/script&gt;
    &lt;script src="js/PagerWidget.js"&gt;&lt;/script&gt;                       
    </code>
</pre>                  
            </li>

            <li>Copy the <a href="https://github.com/evolvingweb/ajax-solr/blob/master/examples/reuters/js/reuters.js" target="_blank">reuters.js</a> file and rename it to, <strong>search.js</strong></li>

            <li>Open <strong>search.js</strong> and remove the following addWidget methods and its corresponding code blocks:
<pre>
    <code>
    var fields = [ 'title', 'id', 'url' ];
    for (var i = 0, l = fields.length; i &lt; l; i++) {
      Manager.addWidget(new AjaxSolr.TagcloudWidget({
        id: fields[i],
        target: '#' + fields[i],
        field: fields[i]
      }));
    }

    Manager.addWidget(new AjaxSolr.CurrentSearchWidget({
      id: 'currentsearch',
      target: '#selection'
    }));

    Manager.addWidget(new AjaxSolr.AutocompleteWidget({
      id: 'text',
      target: '#search',
      fields: [ 'topics', 'organisations', 'exchanges' ]
    }));

    Manager.addWidget(new AjaxSolr.CountryCodeWidget({
      id: 'countries',
      target: '#countries',
      field: 'countryCodes'
    }));

    Manager.addWidget(new AjaxSolr.CalendarWidget({
      id: 'calendar',
      target: '#calendar',
      field: 'date'
    }));

    var params = {
        facet: true,
        'facet.field': ['title', 'id', 'url'],
        'facet.limit': 20,
        'facet.mincount': 1,
        'json.nl': 'map'
    };
    for (var name in params) {
        Manager.store.addByValue(name, params[name]);
    }                   
    </code>
</pre>                  
            </li>

            <li>In <strong>search.js</strong>, update the solrUrl from 'http://evolvingweb.ca/solr/reuters/' to the following:
<pre>
    <code>
    //Old
    solrUrl: 'http://evolvingweb.ca/solr/reuters/'

    //New
    solrUrl: '//localhost:8983/solr/mywebsite/'
    </code>
</pre>              
            </li>

            <li>In <strong>search.js</strong>, update the query from '*:*' wildcard with the following:
<pre>
    <code>
    //Old
    Manager.store.addByValue('q', '*:*');    

    //New
    var userQuery = window.location.search.replace( "?query=", "" );
    Manager.store.addByValue('q', userQuery);
    </code>
</pre>              
            </li>

            <li>In <strong>AbstractManager.js</strong>, update the solrURL (line 34) string to:
<pre>
    <code>
    http://localhost:8983/solr/mywebsite/
    </code>
</pre>                  
            </li>

            <li>In <strong>ResultWidget.js</strong> update lines 46-48 to:
<pre>
    <code>
    items = items.concat(this.facetLinks('title', doc.title));
    items = items.concat(this.facetLinks('id', doc.id));
    items = items.concat(this.facetLinks('url', doc.url));
    </code>
</pre>  
            </li>       

            <li>In <strong>ResultWidget.js</strong> update lines 60-67 to:
<pre>
    <code>
    if (doc.content.length > 300) {
      snippet += ' ' + doc.content.substring(0, 300);
      snippet += '&lt;span style="display:none;"&gt;' + doc.content.substring(300);
      snippet += '&lt;/span&gt; &lt;a href="' + doc.id + '" class="more"&gt;more&lt;/a&gt;';
    }
    else {
      snippet +=  ' ' + doc.content;
    }
    </code>
</pre>  
            </li>       

            <li>In <strong>ResultWidget.js</strong> update line 69 to:
<pre>
    <code>
    var output = '&lt;div&gt;&lt;h2&gt;&lt;a href="' + doc.id + '"&gt;' + doc.title + '&lt;/a&gt;&lt;/h2&gt;';
    </code>
</pre>  
            </li>   

            <li>In <strong>ResultWidget.js</strong> remove line 70:
<pre>
    <code>
    output += '&lt;p id="links_' + doc.id + '" class="links"&gt;&lt;/p&gt;';
    </code>
</pre>  
            </li>   

            <li>In <strong>ResultWidget.js</strong> remove lines 75-91:
<pre>
<code>
    init: function () {
        $(document).on('click', 'a.more', function () {
          var $this = $(this),
              span = $this.parent().find('span');

          if (span.is(':visible')) {
            span.hide();
            $this.text('more');
          }
          else {
            span.show();
            $this.text('less');
          }

          return false;
        });
    }
</code>
</pre>                              
            </li>
        </ol>

        <h3>HINTS:</h3>

        <ol>
            <li>Confirm your AJAX call to the Solr instance:
<pre>
    <code>
1.) Open, Google Chrome > Right click and Inspect Element > Console > Reload the page (if querying 'doctor')
2.) Console should report,  
    Resource interpreted as Script but transferred with MIME type text/plain: "http://localhost:8983/solr/mywebsite/select?q=doctor&wt=json&json.wrf=jQuery171045162516459822655_1383236423015&_=1383236423160". 
    </code>             
</pre>                  
            </li>

        </ol>

    </div>

</div>