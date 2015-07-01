require 'net/ssh'
require 'csv'
require 'formatador'

task :default => :bootstrap

desc "Verify bootstrapped systems"
task :bootstrap do
  title "Verifying the attendees have bootstrapped their systems"
  display verify { file_exists?("/etc/chef/client.pem") }
end

desc "Verifying log level is set"
task :log_level do
  title "Verifying that the attendees have established log level"
  display verify { file_contains?("/etc/chef/client.rb",":info") }
end

desc "Verify setup website"
task :website do
  title "Verifying that the attendees have setup website localhost"
  display verify curl?("localhost")
end

desc "Verify motd file"
task :motd do
  title "Verifying that the attendees have updated the /etc/motd"
  display verify file_has_content?("/etc/motd")
end

desc "Verify setup of second website (port 81)"
task :refactored_website do
  title "Verifying that the attendees have setup a second website on port 81"
  display verify curl?("localhost:81")
end

def verify(command)
  attendees.map do |attendee|
    ssh = Net::SSH.start(attendee.host,user,password: password)
    result = ssh.exec!(command)
    # puts "#{attendee.name} : #{result}"
    { name: attendee.name, progress: !! result }
  end
end

def title(text)
  Formatador.display_line("[white]#{text}[/]")
end

def display(results)
  formatted_results = results.map do |r|
    {
      name: r[:name],
      progress: r[:progress] ? "[green]COMPLETE[/]" : "[red]INCOMPLETE[/]"
    }
  end
  Formatador.display_table(formatted_results)
end

def list_of_attendees
  CSV.open "attendees.csv", headers: true, header_converters: :symbol
end

class Attendee
  attr_accessor :name, :host

  def initialize(name,host)
    @name = name
    @host = host
  end
end

def attendees
  @attendees ||= list_of_attendees.map { |a| Attendee.new(a[:name],a[:host]) }
end

def user
  "chef"
end

def password
  "chef"
end

def file_exists?(filename)
  "if [ -f #{filename} ] ; then echo 'exists' ; fi"
end

def file_contains?(filename,contents)
  "[ -e #{filename} ] && if grep -q #{contents} '#{filename}' ; then echo 'contains' ; fi"
end

def file_has_content?(filename)
  "if [ $(wc -c <#{filename}) -gt 0 ] ; then echo 'content' ; fi"
end

def curl?(site)
  "curl #{site} 2>/dev/null ; if [ $? -eq 0 ] ; then echo 'available' ; fi"
end

def fix_selinux
  "echo 'SELINUX=disabled
SELINUXTYPE=targeted' | sudo tee /etc/selinux/config"
end