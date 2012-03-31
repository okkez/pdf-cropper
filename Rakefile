require 'rake/clean'
require 'prawn'

OPTIONS = {
  :left   => 13.0,
  :right  => 13.0,
  :top    => 10.0,
  :bottom => 5.0,
  :skip   => [1],
}

TARGET = FileList["*.pdf"]
DEST_DIR = "dest"

CLOBBER.push(FileList["dest"])

task :default => :crop

directory DEST_DIR

desc "Crop pdf file in current directory."
task :crop => [DEST_DIR, FileList["*.pdf"]].flatten do |t|
  t.prerequisites.each do |file|
    next if File.directory?(file)
    crop_pdf(file, DEST_DIR, OPTIONS)
  end
end

desc "Force crop pdf file in current directory."
task :crop_force => [DEST_DIR, FileList["*.pdf"]].flatten do |t|
  OPTIONS[:force] = true
  t.prerequisites.each do |file|
    next if File.directory?(file)
    crop_pdf(file, DEST_DIR, OPTIONS)
  end
end

def crop_pdf(filename, dest_dir, options={})
  output_file = File.join(dest_dir, filename)
  if File.exist?(output_file)
    puts "#{output_file} already exists."
    return unless options[:force]
  end
  skip_pages = options[:skip] || []
  Prawn::Document.generate(output_file, template: filename) do
    (1..page_count).each do |n|
      next if skip_pages.include?(n)
      go_to_page(n)
      x1, y1, x2, y2 = page.dictionary.data[:MediaBox]
      x1_new = (x1 + (x2 - x1) * (options[:left]   / 100)).to_i
      x2_new = (x2 - (x2 - x1) * (options[:right]  / 100)).to_i
      y1_new = (y1 + (y2 - y1) * (options[:bottom] / 100)).to_i
      y2_new = (y2 - (y2 - y1) * (options[:top]    / 100)).to_i
      page.dictionary.data[:MediaBox] = [x1_new, y1_new, x2_new, y2_new]
    end
  end
end
