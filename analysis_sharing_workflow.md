# Seamless analysis, presentation and discussion with jupyter notebooks and hypothes.is
I am developing most of my code in jupyter notebooks. So far presenting results (especially at an intermediate stage) has been a total nightmare. Cluttered plot folders on local drives and in the cloud and endless email back and forth seemed like a terrible workflow.

I have decided to adopt a more modern way of presenting and discussing things using jupyter notebook as the central medium.

My examples can be found in a [github repo](https://github.com/jbusecke/reporting_notebooks/blob/master/notebooks/)

There are really only two issues to address:

1)  When using notebooks and git/nbviewer, often times the masses of code obstruct a neat documentation of results and thoughts in the comment cells. We need a sleek way of hiding the code (and bringing it back when needed).

2) Both github and nbviewer render notebooks as a static html site, which is missing the critical ability to comment on results and enable collaboration, without having to fire up the full code each time.

I found a solution to 1) [here](http://chris-said.io/2016/02/13/how-to-make-polished-jupyter-presentations-with-optional-code-visibility/). In order to make the solution for 2) work, the preloader in this source cannot be used at this moment. 
But the basic ‘code-hiding’ ability can be achieved by pasting this code into a raw cell at the beginning of a jupyter notebook:
```
<script>
  function code_toggle() {
    if (code_shown){
      $('div.input').hide('500');
      $('#toggleButton').val('Show Code')
    } else {
      $('div.input').show('500');
      $('#toggleButton').val('Hide Code')
    }
    code_shown = !code_shown
  }

  $( document ).ready(function(){
    code_shown=false;
    $('div.input').hide()
  });
</script>
<form action="javascript:code_toggle()"><input type="submit" id="toggleButton" value="Show Code"></form>

<script>
  $(document).ready(function(){
    $('div.prompt').hide();
    $('div.back-to-top').hide();
    $('nav#menubar').hide();
    $('.breadcrumb').hide();
    $('.hidden-print').hide();
  });
</script>
```


This converts [this sample notebook](https://nbviewer.jupyter.org/github/jbusecke/reporting_notebooks/blob/master/notebooks/TheFirstSteps-RAW.ipynb) into [a really slick presentation](https://nbviewer.jupyter.org/github/jbusecke/reporting_notebooks/blob/master/notebooks/TheFirstSteps-COMMENTS.ipynb). Note this does not work on github, only using the nbviewer.

Now to have the ability to comment, I am using [hypothes.is](https://web.hypothes.is/). I have posted a few dummy comments on the notebook, check them out [here](https://hyp.is/go?url=https%3A%2F%2Fnbviewer.jupyter.org%2Fgithub%2Fjbusecke%2Freporting_notebooks%2Fblob%2Fmaster%2Fnotebooks%2FTheFirstSteps-COMMENTS.ipynb). 

I am using the chrome browser extension at the moment. I am not sure if that is required.
Supposedly they also allow private groups, so you dont have to discuss your research publicly. If you want to try it out, [here](https://hypothes.is/groups/yLY4ZnJb/slack-test-group) is the invitation to a test group.
Let me know what you think!
