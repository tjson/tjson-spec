require "bundler/setup"

TJSON_EXAMPLES = Pathname("draft-tjson-examples.txt")
MANDATORY_KEYS = %w(name description result)

namespace :generate do
  task :xml do
    sh "mmark -xml2 -page draft-tjson-spec.md generated/draft-tjson-spec.xml"
  end

  task txt: :xml do
    sh "xml2rfc --text generated/draft-tjson-spec.xml"
  end

  task all: %w(xml txt)
end

namespace :examples do
  task :verify do
    require "toml"
    require "json"

    examples_text = TJSON_EXAMPLES.read

    # Strip comments
    examples_text.gsub!(/^#.*?$/m, "")

    examples = examples_text.split(/^-----$/m)

    # This is supposed to be blank
    first_example = examples.shift

    unless first_example =~ (/\A\s*\z/)
      fail "Unexpected data before leading '-----': #{first_example.inspect}"
    end

    # This is also supposed to be blank
    last_example = examples.pop

    unless last_example =~ (/\A\s*\z/)
      fail "Unexpected data before trailing '-----': #{last_example.inspect}"
    end

    examples.each_with_index do |example, number|
      metadata_text, example_text, extra = example.split(/^%%%$/m)
      fail "Error parsing header of example \##{number + 1}: extra '%%%'" unless extra.nil?

      # Strip leading newline from metadata
      metadata_text.sub!(/\A\n/m, "")

      begin
        metadata = TOML.parse(metadata_text)
      rescue TOML::ParseError => ex
        fail "Error parsing header of example \##{number + 1}: #{ex.message}"
      end

      metadata.each do |key, _value|
        fail "Unknown key in metadata: #{key} (example \##{number + 1})" unless MANDATORY_KEYS.include?(key)
      end

      MANDATORY_KEYS.each do |key|
        fail "Missing mandatory key from metadata: #{key} (example \##{number + 1})" unless metadata.key?(key)
      end

      result_type = metadata["result"]
      fail "Bad result type: #{result_type.inspect} (example \##{number + 1})" unless %w(success error).include?(result_type)

      if result_type == "success"
        begin
          JSON.parse(example_text)
        rescue JSON::ParserError => ex
          fail "Error parsing example \##{number + 1} as JSON: #{ex.message}"
        end
      end
    end

    puts "Success! Example file '#{TJSON_EXAMPLES}' is valid."
  end
end

task default: %w(examples:verify)
