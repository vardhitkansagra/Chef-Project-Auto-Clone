Chef Automatic Clone Full Project, Create New Repo, Commit And Push Changes
Screenshot

Step 1: Launch AWS Instance and Login
Log in to AWS and create an EC2 instance (Ubuntu).
Download and configure PuTTY for SSH access.
Connect to the EC2 instance using the ubuntu user.
Step 2: Install Chef Workstation in PuTTY Terminal
Switch to the root user:

sudo su
Download Chef Workstation:

wget <chef_workstation_url>
List files to verify the download:

ls
Install the package:

dpkg -i <chef_workstation_package_path>
Check if Chef is installed:

which chef
Step 3: Create Cookbook
Create a cookbooks directory:

mkdir cookbooks
Navigate to the directory:

cd cookbooks
Generate a new cookbook:

chef generate cookbook <cookbook_name>
Step 4: Create Recipe
Navigate into the cookbook directory:

cd <cookbook_name>
Generate a new recipe:

chef generate recipe <recipe_name>
Step 5: Install Tree
Update the system:

apt update && apt upgrade -y
Install the tree package:

apt install tree
Step 6: Configure SSH for GitHub
Navigate to the home directory:

cd ~
Generate an SSH key:

ssh-keygen
Display the public key:

cat ~/.ssh/id_rsa.pub
Add the key to your GitHub account (in GitHub → Settings → SSH and GPG keys).

Test the GitHub SSH connection:

ssh -T git@github.com
Step 7: Update the Recipe File
Edit the recipe file:

nano cookbooks/<cookbook_name>/recipes/<recipe_name>.rb
Replace its contents with the following:

#
# Cookbook:: test
# Recipe:: new-recipe
#

git "/root/rutvik999" do
  repository "git@github.com:rutvik-3377/chef.git"
  revision "main"
  action :sync
end

file "/root/rutvik999/myfile.txt" do
  content "Hello guys."
  action :create
end

execute 'git_add_and_commit' do
  cwd "/root/rutvik999"
  command 'git add . && git commit -m "Automated commit"'
  only_if "test -n \"$(git status --porcelain)\""
end

execute 'checkout_feature-branch' do
  cwd "/root/rutvik999"
  command 'git checkout -b feature-branch || git checkout feature-branch'
end

execute 'push_feature_branch' do
  cwd "/root/rutvik999"
  command 'git push origin feature-branch --force'
end

execute 'checkout_feature-branch' do
  cwd "/root/rutvik999"
  command 'git checkout -b feature-branch || git checkout feature-branch'
end

execute 'merge_feature_into_main' do
  cwd "/root/rutvik999"
  command <<-EOH
    git checkout main &&
    git merge feature-branch
  EOH
  only_if 'git branch --list feature-branch'
end

execute 'push_main_branch' do
  cwd "/root/rutvik999"
  command 'git push origin main'
end
Step 8: Run Chef Client to Apply Changes
Execute the following command to run the Chef recipe:
chef-client -zr "recipe[<cookbook_name>::<recipe_name>]"
