db.define_table('question',
               Field('category',requires=IS_NOT_EMPTY()),               #category of the quiz
               Field('quiz',requires=IS_NOT_EMPTY()),
               Field('problem','text',requires=IS_NOT_EMPTY()),
               Field('problem_number','integer',requires=IS_NOT_EMPTY()),
               Field('answer',requires=IS_NOT_EMPTY()),
               Field('answer_string',requires=IS_NOT_EMPTY()),
               Field('op1',requires=IS_NOT_EMPTY()),
               Field('op2',requires=IS_NOT_EMPTY()),
               Field('op3',requires=IS_NOT_EMPTY()),
               Field('op4',requires=IS_NOT_EMPTY()),
               Field('votes','integer',default=0,readable=False,writable=False))

db.define_table('ac_hldr',
               Field('qid'),
               Field('q_num'),
               Field('quiz'),
               Field('marked',unique=True),
               Field('ans'),
               Field('answer'),
               auth.signature)

db.define_table('vote',
               Field('question','integer'),
               Field('score','integer'),
               auth.signature)

db.define_table('marked_flag',
               Field('qid'),
               Field('flagged'),
               auth.signature)

db.define_table('comm',
               Field('question','integer',readable=False,writable=False),
               Field('your_post','text'),
               auth.signature)

db.define_table('user_ranking',
                Field('u_id'),
                Field('score','integer',default=0))

db.define_table('user_quiz',
               Field('u_id'),
               Field('quiz'))

def written_by(id):
    user = db.auth_user(id)
    return '%(first_name)s %(last_name)s' % user
