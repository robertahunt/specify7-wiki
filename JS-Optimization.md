JS-Optimization
===============

To run the require.js optimizer to build a single JavaScript file for the frontend
use the following command in the top level directory of the source tree:

    node r.js -o frontend/static/js/app.build.js
    
This will produce the file `frontend/static/js/main-built.js` which can be
substituted for `main.js` in `frontend/templates/form.html`.
