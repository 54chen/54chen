title: Function: ignore_integrity()
link: http://www.54chen.com/life/function-ignore_integrity.html
author: cc0cc
description: 
post_id: 301
created: 2008/12/10 11:11:05
created_gmt: 2008/12/10 03:11:05
comment_status: open
post_name: function-ignore_integrity
status: private
post_type: post

# Function: ignore_integrity()

\-- Function: ignore_integrity()   \-- DROP FUNCTION ignore_integrity();   CREATE OR REPLACE FUNCTION ignore_integrity()   RETURNS trigger AS $BODY$ DECLARE     existflag integer; BEGIN     DELETE FROM recommendation where id=NEW.id and recomid=NEW.recomid;     SELECT 1 INTO existflag FROM recomignore WHERE id=NEW.id and recomid=NEW.recomid;     IF FOUND THEN         update recomignore set last_modified=NEW.last_modified, type=NEW.type           where id=NEW.id and recomid=NEW.recomid;         return null;     END IF;     return NEW; END; $BODY$   LANGUAGE 'plpgsql' VOLATILE   COST 100; ALTER FUNCTION ignore_integrity() OWNER TO yahoo;