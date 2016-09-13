def index():
    response.flash = T("Hello World")
    return dict(message=T('Welcome to web2py!'))

@auth.requires_login()
def view_categories():
    categories=[]
    rows = db(db.question.category).select()
    for row in rows:
        if row.category not in categories:
            categories.append(row.category)
    return locals()

@auth.requires_login()
def view_quizzes():
    quizzes = []
    category=request.args(0)
    rows = db(db.question.category==category).select()
    for row in rows:
        if row.quiz not in quizzes:
            quizzes.append(row.quiz)
    return locals()

@auth.requires_login()
def view_questions():
    your_answer=''
    marked=0
    problem_number=request.args(1,cast=int,default=1)
    quiz = request.args(0)
    rows = db(db.question.quiz==quiz).select()
    entries=db(db.ac_hldr).select()
    return locals()

def submit_answer():
    vars = request.post_vars
    marked = vars.marked
    qid = vars.qid
    ans = vars.ans
    quiz = vars.quiz_name
    q_num = vars.q_num
    created_by = vars.created_by
    answer = vars.answer
    exist = db.ac_hldr(qid=qid,created_by=created_by,q_num=q_num)
    if exist:
        exist.update_record(marked=marked,ans=ans,answer=answer,quiz=quiz,q_num=q_num)
    else:
        db.ac_hldr.insert(marked=marked,qid=qid,ans=ans,created_by=created_by,answer=answer,quiz=quiz,q_num=q_num)

def flagging():
    vars = request.post_vars
    flagged = vars.flagged
    qid = vars.qid
    created_by = vars.created_by
    exist = db.marked_flag(qid=qid,created_by=created_by)
    if exist:
        exist.update_record(flagged=flagged)
    else:
        db.marked_flag.insert(flagged=flagged,qid=qid,created_by=created_by)

@auth.requires_login()
def check_answers():
    quiz = request.args(0)
    rows = db(db.ac_hldr.quiz==quiz).select()
    col = db(db.question.quiz==quiz).select()
    return locals()

@auth.requires_login()
def search():
    return dict(form=FORM(INPUT(_id='keyword',_name='keyword', _onkeyup="ajax('callback', ['keyword'], 'target');")),target_div=DIV(_id='target'))

def callback():
    if not request.vars.keyword:return ''
    query = db.question.quiz.contains(request.vars.keyword)
    quiz = db(query).select()
    quizzes = []
    for q in quiz:
        temp=[]
        temp.append(q.quiz)
        temp.append(q.category)
        if temp not in quizzes:
            quizzes.append(temp)
    links = [A(p[0], _href=URL('view_quizzes',args=p[1])) for p in quizzes]
    return DIV(*[DIV(k) for k in links])

def vote_callback():
    vars = request.post_vars
    id = vars.id
    direction = vars.direction
    direction=long(direction)
    post = db.question(id)
    if post:
        vote = db.vote(question=id,created_by=auth.user_id)
        if not vote:
            db.vote.insert(question=id,score=direction)
            post.update_record(votes=post.votes + direction)
        elif vote.score!=direction:
            post.update_record(votes=post.votes+direction*2)
            vote.update_record(score=direction)
    return str(post.votes)

def rating():
    vars=request.post_vars
    u_id=vars.id
    score=vars.score
    score=int(score)
    quiz=vars.quiz
    user=db.user_ranking(u_id=u_id)
    q=db.user_quiz(quiz=quiz,u_id=u_id)
    if not user:
        db.user_ranking.insert(u_id=u_id,score=score)
        db.user_quiz.insert(u_id=u_id,quiz=quiz)
    else:
        if not q:
            user.update_record(score=user.score+score)
            db.user_quiz.insert(u_id=u_id,quiz=quiz)

@auth.requires_membership('appadmin')
def manage():
    grid = SQLFORM.grid(db.question)
    return locals()

@auth.requires_login()
def discussion_forum():
    id = request.args(0,cast=int)
    if auth.user:
        db.comm.question.default = id
        form = SQLFORM(db.comm).process()
    else:
        form = A('((login to comment))', _href=URL('user/login',vars=dict(_next=URL(args=request.args))))
    comments = db(db.comm.question==id).select(orderby=~db.comm.created_on)
    return locals()

def rules():
    #this page will show the rules to go about
    return dict(message=T('Rules are absolute!'))

def user_rankings():
    users = db(db.user_ranking).select(orderby=~db.user_ranking.score)
    return locals()

def user():
    """
    exposes:
    http://..../[app]/default/user/login
    http://..../[app]/default/user/logout
    http://..../[app]/default/user/register
    http://..../[app]/default/user/profile
    http://..../[app]/default/user/retrieve_password
    http://..../[app]/default/user/change_password
    http://..../[app]/default/user/bulk_register
    use @auth.requires_login()
        @auth.requires_membership('group name')
        @auth.requires_permission('read','table name',record_id)
    to decorate functions that need access control
    also notice there is http://..../[app]/appadmin/manage/auth to allow administrator to manage users
    """
    return dict(form=auth())

@cache.action()
def download():
    """
    allows downloading of uploaded files
    http://..../[app]/default/download/[filename]
    """
    return response.download(request, db)

def call():
    """
    exposes services. for example:
    http://..../[app]/default/call/jsonrpc
    decorate with @services.jsonrpc the functions to expose
    supports xml, json, xmlrpc, jsonrpc, amfrpc, rss, csv
    """
    return service()
