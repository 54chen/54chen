title:  recomignore
link: http://www.54chen.com/life/recomignore-2.html
author: cc0cc
description: 
post_id: 297
created: 2008/12/10 11:09:27
created_gmt: 2008/12/10 03:09:27
comment_status: open
post_name: recomignore-2
status: private
post_type: post

#  recomignore

\-- Table: recomignore   \-- DROP TABLE recomignore;   CREATE TABLE recomignore (   tid bigint NOT NULL DEFAULT nextval('seq_ignore'::regclass),   id bigint NOT NULL DEFAULT 0,   recomid bigint NOT NULL DEFAULT 0,   last_modified timestamp without time zone DEFAULT now(),   "type" integer ) WITH (OIDS=FALSE); ALTER TABLE recomignore OWNER TO yahoo;   \-- Index: idx_ignore_id   \-- DROP INDEX idx_ignore_id;   CREATE INDEX idx_ignore_id   ON recomignore   USING btree   (id);   \-- Index: idx_ignore_id_recomid   \-- DROP INDEX idx_ignore_id_recomid;   CREATE UNIQUE INDEX idx_ignore_id_recomid   ON recomignore   USING btree   (id, recomid);     \-- Trigger: recom_ignore_integrity on recomignore   \-- DROP TRIGGER recom_ignore_integrity ON recomignore;   CREATE TRIGGER recom_ignore_integrity   BEFORE INSERT   ON recomignore   FOR EACH ROW   EXECUTE PROCEDURE ignore_integrity();