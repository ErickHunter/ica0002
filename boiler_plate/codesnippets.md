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
  