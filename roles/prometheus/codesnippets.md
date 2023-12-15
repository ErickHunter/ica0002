##These are for taskes

#apt
- name: Install "SERVICE"
  apt:
    name: SERVICE
    state: latest
    update_cache: no

#copy file
- name: Copy file "FROM" to remote host "TO"
  copy:
    src: FROM
    dest: TO/HERE
  notify: Restart SERVICE

- name: Template "WHAT" file "FROM" to remote host "TO"
  template:
    src: FROM
    dest: TO/HERE
    owner: OPTIONAL_UESR
    mode: OPTIONAL
  no_log: ONLY_FOR_THINGS_WITH_SECRETS_IN_TEMPLATE
  notify: HANDLER

#make sure service is started and enabled
- name: Set "SERVICE" state "started" and enabled "yes"
  service:
    name: SERVICE
    state: started
    enabled: yes  



##These are for handelers

#Basic restart Handeler
- name: Restart SERVICE
  service:
    name: SERVICE
    state: restarted
  