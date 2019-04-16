@CrossOrigin
	@GetMapping("/post/comments/{postId}")
	public List<PostComment> retrievePostComment(@PathVariable String postId) {
		
		/**
		 * {
		      id: 1,
		      name: 'root1',
		      children: [
		        { id: 2, name: 'child1' },
		        { id: 3, name: 'child2' }
		      ]
		    }
		 */
		List<PostComment> postComments = postCommentRepository.findByPostId(postId);
		
		Map<String, PostComment> mapTmp = new HashMap<>();
		for (PostComment current : postComments) {
		    mapTmp.put(current.getId(), current);
		}
		
		  
		PostComment entityObject1 =  new PostComment();
        List<PostComment> entityList = new ArrayList<>();
        postComments.stream().forEach(p->{
        	PostComment parentEntity= mapTmp.get(p.getParentId());
        	if(parentEntity!=null){
        		
    			List<PostComment> children = new ArrayList<>();
    			for(PostComment ent : postComments){
    				if(parentEntity.getId().equals(ent.getParentId())){
    					children.add(ent);
					}
    			} 
    			parentEntity.setChildren(children);
				entityList.add(parentEntity);
    			entityObject1.setChildren(entityList);
    		}
        	
        	 for (PostComment current : entityList) {
                System.out.println(current.getPostComment() + ", ParentId: "+ current.getParentId());
             }
        	
        }); 

		//return new ResponseEntity<Entity>(entityObject1.getEntityTree(),HttpStatus.OK);
		
		return postComments;
		
	}
  
  
  Angular Code:
  postComment(event,post){
   
    if(event.keyCode == 13) {
      console.log(this.message);
      let postComment = {
        "postId":post.id,
        "postComment":this.message,
        "parentId":0
      }
      this.authService.postComment(postComment).subscribe(data=>{     
       // this.comments = data;
       this.fetchCommentsByPostId(post.id);
      })
  }
  }

  fetchCommentsByPostId(postId:string){
    this.authService.fetchCommentsByPostId(postId).subscribe(data=>{
      this.comments = data;
    })
  }

/*   postReply(post,reply){
    console.log(this.message);
    
    let postComment = {
      "postId":post.id,
      "postComment":this.CommwentMessage,
      "parentId":postComment.id
    }

  } */


  
  
  Tree View:
  //https://angular2-tree.readme.io/docs/
  //https://pusher.com/tutorials/realtime-table-angular
  //https://beyondscheme.com/2016/angular2-discussion-portal
  
