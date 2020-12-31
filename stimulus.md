Step 6 — Creating the Stimulus Controller
Installing Stimulus created the app/javascript/controllers directory, which is where webpack is loading our application context from, so we will create our posts controller in this directory. This controller will include each of the methods we referenced in the previous step: - addBody() , to add new posts. - showAll() , to show older posts. - remove() , to remove posts from the current view. - upvote() , to attach an upvote icon to posts.
Create a file called posts_controller.js in the ers directory:
First, at the top of the file, extend Stimulus's built-in Controller class:
         nano app/javascript/controllers/posts_controller.js
  </div>
</div>
  app/javascript/controll
     
    Next, add the following target definitions to the file:
 ~/sharkapp/app/javascript/controllers/posts_cont
roller.js
  .. .
export default class extends Controller {
    static targets = ["body", "add", "show"]
}
Defining targets in this way will allow us to access them in our methods with the this.target-nameTarget property, which gives us the first matching target element. So, for example, to match the body data target defined in our targets array, we would use this.bodyTarget . This property allows us to manipulate things like input values or css styles.
Next, we can define the addBody method, which will control the appearance of new posts on the page. Add the following code below the target definitions to define this method:
    ~/sharkapp/app/javascript/controllers/posts_cont
roller.js
 import { Controller } from "stimulus"
export default class extends Controller {
}
 
    This method defines a content variable with the let keyword and sets it equal to the post input string that users entered into the posts form. It does this by virtue of the body data target that we attached to the <textarea> element in our form. Using this.bodyTarget to match this element, we can then use the value property that is associated with that element to set the value of content as the post input users have entered.
Next, the method adds this post input to the add target we added to the <ul > element below the form builder in the sharks/posts partial. It does this using the Element.insertAdjacentHTML() method, which will insert the content of the new post, set in the content variable, before the add target element. We've also enclosed the new post in an <li> element, so that new posts appear as bulleted list items.
              ~/sharkapp/app/javascript/controllers/posts_cont
roller.js
 .. .
export default class extends Controller {
    static targets = [ "body", "add", "show"]
    addBody() {
        let content = this.bodyTarget.value;
        this.addTarget.insertAdjacentHTML('beforebegin', "<li
>" + content + "</li>");
    }
}
 
 Next, below the addBody method, we can add the showAll method, which will control the appearance of older posts on the page:
   ~/sharkapp/app/javascript/controllers/posts_cont
roller.js
  .. .
export default class extends Controller { .. .
    addBody() {
        let content = this.bodyTarget.value;
        this.addTarget.insertAdjacentHTML('beforebegin', "<li
>" + content + "</li>");
    }
    showAll() {
        this.showTarget.style.visibility = "visible";
}
}
Here, we again use the this.target-nameTarget property to match our sho w target, which is attached to the <div> element with the sharks/all partial. We gave it a default style, "visibility:hidden" , so in this method, we simply change the style to "visible" . This will show the partial to users who have opted into seeing older posts.
       
 Below showAll, we'll next add an upvote method, to allow users to “upvote” posts on the page by attaching the free Font Awesome
le icon to a particular post.
Add the following code to define this method:
    ~/sharkapp/app/javascript/controllers/posts_cont
roller.js
  .. .
export default class extends Controller { .. .
    showAll() {
        this.showTarget.style.visibility = "visible";
}
    upvote() {
        let post = event.target.closest(".post");
        post.insertAdjacentHTML('beforeend', '<i class="fa fa-
check-circle"></i>');
    }
}
Here, we're creating a post variable that will target the closest <li> element with the class post — the class we attached to each <li> element
      check-circ
  
 in our loop iteration in sharks/all . This will target the closest post and add the check-circle icon just inside <li> element, after its last child.
Next, we'll use a similar method to hide posts on the page. Add the following code below the upvote method to define a remove method:
      ~/sharkapp/app/javascript/controllers/posts_cont
roller.js
  .. .
export default class extends Controller { .. .
    upvote() {
        let post = event.target.closest(".post");
        post.insertAdjacentHTML('beforeend', '<i class="fa fa-
check-circle"></i>');
    }
    remove() {
        let post = event.target.closest(".post");
        post.style.visibility = "hidden";
}
}
Once again, our post variable will target the closest <li> element with the class post. It will then set the visibility property to "hidden" to hide the
    
  post on the page.
The finished controller file will now look like this:

~/sharkapp/app/javascript/controllers/posts_cont
roller.js
   import { Controller } from "stimulus"
export default class extends Controller {
    static targets = ["body", "add", "show"]
    addBody() {
        let content = this.bodyTarget.value;
        this.addTarget.insertAdjacentHTML('beforebegin', "<li
>" + content + "</li>");
    }
    showAll() {
        this.showTarget.style.visibility = "visible";
}
    upvote() {
        let post = event.target.closest(".post");
        post.insertAdjacentHTML('beforeend', '<i class="fa fa-
check-circle"></i>');
    }
    remove() {
        let post = event.target.closest(".post");
 
         post.style.visibility = "hidden";
    }
}
 link_to
index
 index index
   nano app/views/sharks/index.html.erb
  button_to
javascript_pack_tag
    vascript_include_tag
    app/views/layouts/app
   lication.html.erb
    Save and close the file when you are finished editing.
With your Stimulus controller in place, you can move on to making some final changes to the view and testing your application.
