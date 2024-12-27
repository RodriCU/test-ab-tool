<h1>Free Test AB tool (beta)</h1>
Google Optimize disappeared and access to online experimentation became more difficult for many companies.

Alternatives such as test launching with GTM came back.

The problem?

The impossibility to manage traffic distribution (among others).

So curiosity led me to try to find a solution. I started talking to Chat GPT, reading articles, various documentation and exploring solutions.

<strong>In this article I tell you step by step how to set up a free experimentation tool.</strong>

It is a beta, it is not a friendly process and it is infinitely improvable. That's why I make it public, so that anyone can contribute their knowledge and improve it.

<i>If you are a programmer and your eyes bleed when you see the codes that I expose is the most normal thing in the world (I am not a developer).

For this reason I make them public, because I lack time but above all knowledge to continue improving the tool.

Can you help me? 🙏</i>

<strong>Index:</strong>
<ol>
  <li><a href="https://github.com/RodriCU/test-ab-tool?tab=readme-ov-file#what-is-the-challenge">What is the challenge?</a></li>
  <li><a href="https://github.com/RodriCU/test-ab-tool?tab=readme-ov-file#step-by-step-implementation">Step by step impplementation</a></li>
  <ol>
    <li><a href="https://github.com/RodriCU/test-ab-tool?tab=readme-ov-file#google-sheet-api-from-google-cloud">Google Sheet API from Google Cloud</a></li>
    <li><a href="https://github.com/RodriCU/test-ab-tool?tab=readme-ov-file#google-sheet-as-a-database">Google Sheet as a data base</a></li>
    <li><a href="https://github.com/RodriCU/test-ab-tool?tab=readme-ov-file#avoiding-the-flicker-effect">Avoiding flicker effect</a></li>
    <li><a href="https://github.com/RodriCU/test-ab-tool?tab=readme-ov-file#script-that-controls-the-logic-of-the-experiment">Script that controls the logic of the experiment</a></li>
    <li><a href="https://github.com/RodriCU/test-ab-tool?tab=readme-ov-file#script-that-generates-a-new-alternative-version">Script that generates a new alternative version</a></li>
    <li><a href="https://github.com/RodriCU/test-ab-tool?tab=readme-ov-file#measurement-via-data-layer-and-external-tools">Measurement via data layer and external tools</a></li>
    <li><a href="https://github.com/RodriCU/test-ab-tool?tab=readme-ov-file#useful-scripts">Useful scripts</a></li>
  </ol>  
</ol>



<h2>What is the challenge?</h2>
<ul>
  <li><strong>To have a free or low-cost solution to validate hypotheses with real traffic.</strong>strong> At the moment the tool is promising.
</li>
  <li><strong>That we can trust the results.</strong>strong> In different tests I have performed on a website with real traffic, I have not detected any problems with the data.
</li>
  <li><strong>That the implementation is as agile as possible.</strong>strong> Today, in my opinion, this is the big problem.
</li>
  <li><strong>The launching of the experiments should be as agile as possible.</strong>strong> Relative difficulty, if we are given access via FTP to a JS file that will be responsible for making the layout changes well, but we can find barriers in certain companies.
</li>
  <li><strong>Open source.</strong>strong> That anyone can make it work.
</li>
</ul>



<h2>Step-by-step implementation</h2>
<h3>Google Sheet API from Google Cloud</h3>
The first thing to do is to create a Google Sheets API from a Google Cloud project. The API key will be used in the code of the <a target="_blank" href="https://github.com/RodriCU/test-ab-tool/blob/main/user-control">user-control file</a>.

The API has certain limitations that can condition the entry of users to the experiment but they do not have to be a problem for most projects.


<h3>Google Sheet as a database</h3>
The biggest problem with the thousand articles about online experimentation with Google Tag Manager lies in not being able to control that the traffic distribution is as configured, the usual 50% - 50% (or whatever).

That was the big problem to solve and that can be addressed with a simple Google Sheets to avoid having to get into creating a database.

Through this video by Julian Juenemann I found “the hint” that we will use to dump data from our script hosted in the back to Google Sheet.

Watch it, follow the steps and continue:
https://www.youtube.com/watch?v=in92sz0rvHw


The codes to be used in Apps scripts are:
<ul>
  <li><a target="_blank" href="https://github.com/RodriCU/test-ab-tool/blob/main/sheet-srm-control">sheet-srm-control</a></li>
  <li><a target="_blank" href="https://github.com/RodriCU/test-ab-tool/blob/main/sheet-remove-duplicates">sheet-remove-duplicates</a></li> (in case you want to prevent reaching row limits)
</ul>


The ID of the Sheet will be used in the user-control file so that it can be consulted if the distribution is 50%-50% or if it is necessary to divert traffic to one of the alternatives in the experiment to ensure that there is no SRM problem.


<h3>Avoiding the Flicker Effect</h3>
The flicker effect occurs when in an ab test the user sees the original version for an instant before viewing the alternative version.

First we will test without anti flicker to evaluate if the effect occurs or not. If we see such effect, we will add a CSS style as high as possible within the tag and a small script that will allow us to control the milliseconds that the screen appears blank until the version of the page is displayed.

In this way we give time for the changes of the test ab to be rendered.

In this code in the <a target="_blank" href="https://github.com/RodriCU/test-ab-tool/blob/main/anti-flicker">anti-flicker file</a>, it is configured so that the delay is 800 milliseconds.

This figure can be reduced or increased depending on how long it takes to execute the changes of the experiment and how is the rendering of the web where we are working.


<h3>Script that controls the logic of the experiment</h3>
In order to run the experiment we need to execute a script that assigns a cookie to the user with each request to the server and that respects the proportion of traffic that each version has to receive, for which we will use a Google Cloud API (the one you have created previously) for Spreadsheets and a Google Sheets file (which you have also created).

<strong>The code in question is developed in PHP language since I have made the tests in a WordPress, however you can apply the same logic in any language.</strong>

As previously said, this code, in <a target="_blank" href="https://github.com/RodriCU/test-ab-tool/blob/main/user-control">user-control file</a>, is very optimizable at the development level, any idea is welcome.

In this step I had quite a few problems with caching systems, in the end, at least on the website where I tested, they seem to be solved. Keep this in mind in order to perform validations on the performance of the tool, this can be a great stopper.

In this image you can see how the data would be filled in the Google Sheet:
https://cro.school/wp-content/uploads/2024/12/14-12-2024_20-50-46.png

<h4><strong>The key columns are G, H and I.</strong></h4>

In columns G and H the users that enter in the original and variant version are added.

In I, both quantities are subtracted. So the following happens in the back script:
<ul>
  <li><strong>If the value is 0:</strong> a random calculation is executed by which chance determines whether the next user sees one version or the other.
</li>
  <li><strong>If the value is 1:</strong> the balance is balanced and the next user will see the variant to guarantee 50%-50%.
</li>
  <li><strong>If the value is -1:</strong> the original version will be shown.
</li>
</ul>


<h3>Script that generates a new alternative version</h3>
In order to run the experiment we need to upload a Javascript file to the client server which is called from the opening <head> tag as high as possible, just after the system opening HTML tag and after the anti flicker system, <a target="_blank" href="https://github.com/RodriCU/test-ab-tool/blob/main/js-location">here an example in js-location file</a>.

And what about the visual editor? There is none of that, we make the changes in the DOM with Javascript. Here is an example of an experiment on the web where I tested <a target="_blank" href="https://github.com/RodriCU/test-ab-tool/blob/main/js-changes-example">the code used to perform an A/A test of the tool's behavior</a>.


<h3>Measurement via data layer and external tools</h3>
And how do we measure the results? The idea I had was with our beloved and hated GA4 via data layer (<a target="_blank" href="https://github.com/RodriCU/test-ab-tool/blob/main/data-layer">code example</a>).

In advanced projects, by sending to GA4 we will be able to base all our analysis on BigQuery, although there are some aspects that we will have to take into account explained in this publication.

To this we add external tools such as:
<ul>
  <li>https://speero.com/ab-test-calculator</li>
  <li>https://abtestguide.com/calc/</li>
  <li>https://abtestguide.com/bayesian/</li>
</ul>


<h3>Useful scripts</h3>
Here some drafts of possible ways to narrow the <a target="_blank" ahref="https://github.com/RodriCU/test-ab-tool/blob/main/audience">audience of experiments</a>:
<ul>
  <li>By devices.</li>
    <li>By referrer url.</li>
    <li>By UTM.</li>
    <li>By data layer in first nesting.</li>
    <li>By data layer in second nesting (ecommerce).</li>
    <li>By cookie.</li>
</ul>

Others?,do you need more?






